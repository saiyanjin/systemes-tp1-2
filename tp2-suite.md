# TP 02 Suite : Services, processus signaux

## 2 Processus

### 2.1 Exercice : Etude des processus UNIX

**1. j'ai utilisé la commande `ps axu`**

- c'est le temps d'exécution de la commande 

- c'est l'appel de la commande ps axu qui m'indique 100 dans %CPU :
```
root        1068  100  0.2   9520  4264 pts/0    R+   09:58   0:00 ps axu
```

- c'est le processus qui se situe dans /sbin/init :
```
root           1  0.0  0.7  23680 14600 ?        Ss   09:02   0:01 /sbin/init
```

- ma machine a démarré à 9h02 :
```
root           1  0.0  0.7  23680 14600 ?        Ss   09:02   0:01 /sbin/init
```
Autre commande : 

- je dirai 1262 en partant du principe que le dernier PID est le dernier processus créé en date 
```
root        1262  0.0  0.2   9520  4212 pts/0    R+   10:12   0:00 ps axu
```

**2. j'ai utilisé la commande `ps -eF`**

- tous les processus ancêtres de la commande listés `ps -eF` :
```
root        1277    1008  0  2384  4384   0 10:14 pts/0    00:00:00 ps -eF
```
- la commande 1008 : `root        1008    1007  0  2182  5600   0 09:55 pts/0    00:00:00 -bash`
- la commande 1007 : `root        1007     999  0  4971  7312   0 09:55 ?        00:00:01 sshd-session: root@pts/0`
- la commande 999 : `root         999     696  0  4940 12672   0 09:54 ?        00:00:00 sshd-session: root [priv]`
- la commande 696 : `root         696       1  0  2941  7920   0 09:02 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups`
- la commande 1 : `root           1       0  0  5920 14724   0 09:02 ?        00:00:01 /sbin/init`

**3. j'ai utilisé la commande `pstree -h` :**
```
root@serveur1:~# pstree -h
systemd─┬─cron
        ├─dbus-daemon
        ├─dhcpcd─┬─dhcpcd───4*[dhcpcd]
        │        └─2*[dhcpcd]
        ├─login───bash
        ├─sshd───sshd-session───sshd-session───bash───pstree
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-timesyn───{systemd-timesyn}
        ├─systemd-udevd
        └─wpa_supplicant
```
		
- on obtient l'arborescence de la commande pstree : `sshd───sshd-session───sshd-session───bash───pstree`

**4.**

- `top` puis M (maj + m)

- le processus le plus gourmand est : `       1 root      20   0   23680  14724  10720 S   0,0   0,7   0:01.34 systemd`
- d'après man systemd, i ls'agit du gestionnaire de services et de systèmes d'exploitation Linux.

- z pour activer/désactiver la couleur et Z (maj + z) pour configurer la couleur au choix en changeant la couleur avec les flèches
- R (maj + r) pour changer la colonne de tri

- htop -> 

## 3 Exercice 2 : Arrêt d'un processus

- CTRL-Z : Suspend le processus (signal SIGTSTP)
- jobs : Liste les processus en arrière-plan du shell actuel
- fg %n : Ramène le job n au premier plan (foreground)
- CTRL-C : Envoie le signal SIGINT pour interrompre le processus
- kill PID : Envoie le signal SIGTERM (arrêt propre)
- kill -9 PID : Envoie le signal SIGKILL (arrêt forcé)

## 4 Exercice 3 : les tubes

**cat :**
- Affiche le contenu d'un ou plusieurs fichiers
- Sans argument, lit depuis l'entrée standard
- Peut rediriger la sortie vers un fichier avec >

**tee :**
- Lit depuis l'entrée standard
- Écrit en même temps vers :
  * La sortie standard -> affichage à l'écran
  * Un ou plusieurs fichiers


**la commande `ls | cat` :**

- ls génère la liste des fichiers du répertoire courant
- Le "|" envoie cette sortie à cat
- cat affiche cette liste
- Affiche la liste des fichiers


**la commande `ls -l | cat > liste` :** 

- ls -l génère une liste détaillée des fichiers
- cat reçoit cette liste via le "|"
- La redirection > enregistre le tout dans le fichier "liste"
- Crée un fichier "liste" contenant la sortie de ls -l


**la commande `ls -l | tee liste` :**

Explication :
- ls -l génère une liste détaillée des fichiers
- tee reçoit cette liste via le "|"
- tee écrit simultanément :
  * Dans le fichier "liste"
  * Sur la sortie standard
- Affiche la liste à l'écran & sauvegarde la même liste dans le fichier "liste"


**la commande `ls -l | tee liste | wc -l` :**

- ls -l génère une liste détaillée des fichiers
- tee reçoit cette liste et :
  * L'enregistre dans le fichier "liste"
  * L'envoie au prochain pipe
- wc -l compte le nombre de lignes reçues
- Affiche le nombre de lignes & sauvegarde la liste complète dans "liste"

## 5 Journal système rsyslog
