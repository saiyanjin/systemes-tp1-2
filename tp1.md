# TP1 – Administration Système Debian

## 2.5 Vérifications système

### Locales

```bash
root@serveur1:~# echo $LANG
fr_FR.UTF-87
```

### Nom de la machine

```bash
root@serveur1:~# hostname
serveur1
```

### Domaine

```bash
root@serveur1:~# hostname -d
ufr-info-p6.jussieu.fr
```

### Vérification de l’emplacement des dépôts APT

```bash
root@serveur1:~# cat /etc/apt/sources.list | grep -v -E '^#|^$'
deb http://deb.debian.org/debian/ trixie main
deb http://security.debian.org/debian-security trixie-security main
deb http://deb.debian.org/debian/ trixie-updates main
```

### Fichiers `/etc/shadow` et `/etc/passwd`

#### `/etc/shadow`

```bash
root@serveur1:~# cat /etc/shadow | grep -vE ':\*:|:!\*:'
root:$y$j9T$rMbUvoohoP1uKYQVWi4FS1$IB4Rd4CRQL.VxQbu9eYJ3/1A344RFb/l/cSyFS0RdDD:20464:0:99999:7:::
dhcpcd:!:20464::::::
```

#### `/etc/passwd`

```bash
root@serveur1:~# cat /etc/passwd | grep -vE 'nologin|sync'
root:x:0:0:root:/root:/bin/bash
dhcpcd:x:100:65534:DHCP Client Daemon:/usr/lib/dhcpcd:/bin/false
```

---

### Commandes `fdisk -l` et `fdisk -x`

#### `fdisk -l`

Cette commande affiche la liste des disques et de leurs partitions avec des informations générales (taille, type, secteurs, etc.).

```bash
root@serveur1:~# fdisk -l
Disque /dev/sda : 20 GiB, 21474836480 octets, 41943040 secteurs
Modèle de disque : VBOX HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Type d'étiquette de disque : gpt

Périphérique    Début      Fin Secteurs Taille Type
/dev/sda1        2048 19531775 19529728   9,3G Système de fichiers Linux
/dev/sda2    19531776 27344895  7813120   3,7G Système de fichiers Linux
/dev/sda3    27344896 29298687  1953792   954M Système de fichiers Linux
/dev/sda4    29298688 41940991 12642304     6G Partition d'échange Linux
```

#### `fdisk -x`

Cette commande renvoie une vue détaillée du disque, incluant les UUID, noms de partitions et attributs GPT.

```bash
root@serveur1:~# fdisk -x
Disque /dev/sda : 20 GiB, 21474836480 octets, 41943040 secteurs

Périphérique    Début      Fin Secteurs Type-UUID                            UUID                                 Nom          Attr.
/dev/sda1        2048 19531775 19529728 0FC63DAF-8483-4772-8E79-3D69D8477DE4 411BDF41-3270-4DD5-B55C-753131E84F6F la racine    LegacyBIOSBootable
/dev/sda2    19531776 27344895  7813120 0FC63DAF-8483-4772-8E79-3D69D8477DE4 C9E875AA-880E-47E3-A0A4-7342A164371F espace tempo
/dev/sda3    27344896 29298687  1953792 0FC63DAF-8483-4772-8E79-3D69D8477DE4 F136794F-D214-42E2-A2B5-462449B91F75 les logs
/dev/sda4    29298688 41940991 12642304 0657FD6D-A4AB-43C4-84E5-0933C84B4F4F D18D9E11-C8CE-480D-B0E5-18B8E950AAAB ma swap
```

### Commande `df -h`

La commande `df -h` affiche l’espace disque utilisé et disponible sur les systèmes de fichiers avec les tailles visibles.

---

## 3 Questions théoriques

### 3.1 Preseed

Le *preseed* sert à automatiser l’installation de Debian en fournissant à l’avance les réponses aux questions de l’installateur.

### 3.2 Mot de passe root oublié

Il est possible de démarrer en **mode rescue** pour accéder à un environnement permettant de modifier le mot de passe root.

### 3.3 Redimensionnement de la partition racine

Il est possible de redimensionner la partition racine sans réinstaller en utilisant un live CD/USB pour démonter la partition et ajuster sa taille.

### 3.4
