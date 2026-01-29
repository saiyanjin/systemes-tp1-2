# TP2 – Accès SSH et gestion des clés

## 1.4 Connexion SSH avec clé

### Commande utilisée

```powershell
PS C:\Users\flame\.ssh> ssh -i id_rsa.pub root@10.228.220.30
```

### Erreur rencontrée

```text
Bad permissions. Try removing permissions for user: \\Tout le monde (S-1-1-0) on file C:/Users/flame/.ssh/id_rsa.pub.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions for 'id_rsa.pub' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "id_rsa.pub": bad permissions
```

## 1.5 Sécurisation de l’accès SSH

### Accès root par clé uniquement

Pour sécuriser l’accès SSH au compte **root** et éviter les attaques par force brute, il est recommandé d’autoriser **uniquement l’authentification par clé SSH** et de désactiver l’authentification par mot de passe.

#### Procédure

1. Générer une paire de clés SSH sur le poste client (si ce n’est pas déjà fait) :

```bash
ssh-keygen
```

2. Copier la clé publique sur le serveur :

```bash
ssh-copy-id root@10.228.220.30
```

3. Modifier la configuration SSH sur le serveur :

```bash
nano /etc/ssh/sshd_config
```

Paramètres recommandés :

```text
PermitRootLogin prohibit-password
PasswordAuthentication no
PubkeyAuthentication yes
```

4. Redémarrer le service SSH :

```bash
systemctl restart ssh
```

Ainsi, le compte root ne peut plus se connecter qu’à l’aide d’une clé SSH valide.

---

### Attaques de type brute-force SSH

Une attaque **brute-force SSH** consiste à tester automatiquement un très grand nombre de combinaisons de mots de passe sur un serveur SSH jusqu’à trouver le bon.

Ces attaques sont généralement réalisées par des scripts ou des bots et exploitent :

* des mots de passe faibles,
* des comptes connus (root, admin),
* des serveurs exposés directement sur Internet.

Les conséquences peuvent être graves : compromission du serveur, vol de données, installation de logiciels malveillants.

---

### Autres techniques de protection contre le brute-force

#### 1. Utilisation de comptes non-root

Créer des comptes utilisateurs standards et interdire la connexion SSH directe de root :

```text
PermitRootLogin no
```

L’administration se fait ensuite via `sudo`.

**Avantage** : meilleure traçabilité des actions.

---

#### 2. Changement du port SSH

Modifier le port SSH par défaut (22) pour réduire les scans automatiques.

```text
Port 2222
```

**Avantage** : diminue le bruit des attaques automatisées.

**Inconvénient** : ce n’est pas une vraie sécurité, seulement une protection supplémentaire.

---

#### 3. Fail2ban

Fail2ban bloque automatiquement les adresses IP après plusieurs tentatives de connexion échouées.

**Avantage** : protection efficace et automatisée.

**Inconvénient** : nécessite une configuration et une surveillance.

---

#### 4. Pare-feu (iptables / nftables)

Limiter l’accès SSH à certaines adresses IP ou réseaux.

**Avantage** : très efficace contre les attaques externes.

**Inconvénient** : peu flexible si l’administrateur change souvent de réseau.

---

### Conclusion

La combinaison la plus efficace consiste à :

* utiliser l’authentification par clé SSH,
* désactiver les mots de passe,
* limiter ou interdire l’accès root direct,
* ajouter Fail2ban et un pare-feu.

Ces méthodes offrent un bon compromis entre sécurité et facilité d’administration.
