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

Pour sécuriser l’accès SSH au compte **root** et éviter les attaques par force brute, il faut autoriser **uniquement l’authentification par clé SSH** et de désactiver l’authentification par mot de passe.

#### Procédure

1. Générer une paire de clés SSH :

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

4. Redémarrer le service SSH :

```bash
systemctl restart ssh
```

---

### Attaques de type brute-force SSH

Une attaque **brute-force SSH** est une attaque qui va tester automatiquement un très grand nombre de combinaisons de mots de passe sur un serveur SSH jusqu’à trouver le bon.
