TP 03 : Shell Bash

---

## Exercice 1 : Paramètres

```bash
#!/bin/bash

echo "Bonjour, vous avez rentré $# paramètres."
echo "Le nom du script est $0"
echo "Le 3ème paramètre est $3"
echo "Voici la liste des paramètres : $@"
```

## Exercice 2 : Vérification du nombre de paramètres

```bash
#!/bin/bash

# verif nb parametres
if [ $# -ne 2 ]; then
    echo "Erreur : 2 paramètres attendus"
    exit 1
fi

# concatenation
CONCAT="$1$2"

# afficher resultat
echo "Résultat : $CONCAT"
```

## Exercice 3 : Type et droits d'un fichier

```bash
#!/bin/bash

# verif nb parametres
if [ $# -ne 1 ]; then
    echo "Erreur : vous devez fournir exactement 1 paramètre."
    exit 1
fi

FICHIER="$1"
USER=$(whoami)

# verif si ça existe
if [ ! -e "$FICHIER" ]; then
    echo "Le fichier $FICHIER n'existe pas."
    exit 1
fi

# type du fichier
if [ -d "$FICHIER" ]; then
    echo "Le fichier $FICHIER est un répertoire"
elif [ -f "$FICHIER" ]; then
    if [ -s "$FICHIER" ]; then
        echo "Le fichier $FICHIER est un fichier ordinaire qui n'est pas vide"
    else
        echo "Le fichier $FICHIER est un fichier ordinaire vide"
    fi
else
    echo "Le fichier $FICHIER est d'un autre type"
fi

# droits
DROITS=""

[ -r "$FICHIER" ] && DROITS="$DROITS lecture"
[ -w "$FICHIER" ] && DROITS="$DROITS écriture"
[ -x "$FICHIER" ] && DROITS="$DROITS exécution"

if [ -z "$DROITS" ]; then
    echo "\"$FICHIER\" n'est accessible par $USER avec aucun droit"
else
    echo "\"$FICHIER\" est accessible par $USER en$DROITS"
fi
```

## Exercice 4 : Afficher le contenu d'un répertoire

```bash
#!/bin/bash

# verif nb parametres
if [ $# -ne 1 ]; then
    echo "Erreur : vous devez fournir exactement 1 paramètre."
    exit 1
fi

REP="$1"

if [ ! -d "$REP" ]; then
    echo "Erreur : $REP n'est pas un répertoire."
    exit 1
fi

echo "####### fichiers dans $REP/"
for elem in "$REP"/*; do
    if [ -f "$elem" ]; then
        echo "$elem"
    fi
done

echo "####### repertoires dans $REP/"
for elem in "$REP"/*; do
    if [ -d "$elem" ]; then
        echo "$elem"
    fi
done
```

## Exercice 5 : Lister les utilisateurs

```bash
#!/bin/bash

awk -F: '$3 > 100 { print $1 }' /etc/passwd
```

## Exercice 6 : Mon utilisateur existe-t-il

```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    exit 1
fi

ARG="$1"

if [[ "$ARG" =~ ^[0-9]+$ ]]; then
    # recherche dans /etc/passwd par UID
    USER_LINE=$(awk -F: -v uid="$ARG" '$3 == uid {print $0}' /etc/passwd)
    
    if [ -n "$USER_LINE" ]; then
        echo "$ARG"
    fi

else
    # sinon c'est un login
    USER_LINE=$(grep "^$ARG:" /etc/passwd)
    
    if [ -n "$USER_LINE" ]; then
        UID=$(echo "$USER_LINE" | cut -d: -f3)
        echo "$UID"
    fi
fi
```

## Exercice 7 : Création d'un utilisateur

```bash
#!/bin/bash

# verif que le script est exécuté en tant que root
if [ "$USER" != "root" ]; then
    echo "Erreur : ce script doit être exécuté en tant que root."
    exit 1
fi

# saisie des informations
echo -n "Login : "
read LOGIN

echo -n "Nom : "
read NOM

echo -n "Prénom : "
read PRENOM

echo -n "UID : "
read UID_NEW

echo -n "GID : "
read GID_NEW

echo -n "Commentaires : "
read COMMENTAIRES

# verif que le login n'existe pas déjà
EXISTING=$(./exists-user.sh "$LOGIN")
if [ -n "$EXISTING" ]; then
    echo "Erreur : l'utilisateur '$LOGIN' existe déjà (UID: $EXISTING)."
    exit 1
fi

# verif que le répertoire home n'existe pas déjà
HOME_DIR="/home/$LOGIN"
if [ -d "$HOME_DIR" ]; then
    echo "Erreur : le répertoire $HOME_DIR existe déjà."
    exit 1
fi

# création du user
useradd -u "$UID_NEW" -g "$GID_NEW" -c "$PRENOM $NOM - $COMMENTAIRES" -m -d "$HOME_DIR" "$LOGIN"

if [ $? -eq 0 ]; then
    echo "Utilisateur '$LOGIN' créé avec succès (UID=$UID_NEW, home=$HOME_DIR)."
else
    echo "Erreur lors de la création de l'utilisateur."
    exit 1
fi
```

## Exercice 8 : Lecture au clavier

### Question : commandes `read`, `file`, `more`

**`read`** permet de lire une saisie clavier et de l'affecter à une variable :

```bash
echo -n "Entrer votre nom: "
read nom
echo "Votre nom est $nom"
```

**`file`** analyse le contenu d'un fichier et affiche son type réel (indépendamment de l'extension) :

```
$ file /etc/passwd
/etc/passwd: ASCII text

$ file /bin/bash
/bin/bash: ELF 64-bit LSB pie executable, ...
```

**`more`** — raccourcis clavier :

-> Quitter : `q`
-> Avancer d'une ligne : `Entrée`
-> Avancer d'une page : `Espace`
-> Remonter d'une page : `b`
-> Chercher une chaîne : `/chaîne` puis `Entrée`
-> Occurrence suivante : `n`

### Script

```bash
#!/bin/bash

# verif nb parametres
if [ $# -ne 1 ]; then
    echo "Erreur : vous devez fournir exactement 1 paramètre."
    exit 1
fi

REP="$1"

if [ ! -d "$REP" ]; then
    echo "Erreur : $REP n'est pas un répertoire."
    exit 1
fi

for elem in "$REP"/*; do
    if [ ! -f "$elem" ]; then
        continue
    fi

    # verif si c'est bien un fichier texte 
    TYPE=$(file "$elem")
    if echo "$TYPE" | grep -q "text"; then
        echo -n "Voulez-vous visualiser le fichier $elem ? [o/n] "
        read REPONSE
        if [ "$REPONSE" = "o" ] || [ "$REPONSE" = "O" ]; then
            more "$elem"
        fi
    fi
done
```

## Exercice 9 : Appréciation

```bash
#!/bin/bash

while true; do
    echo -n "Entrez une note (ou q pour quitter) : "
    read NOTE

    if [ "$NOTE" = "q" ] || [ "$NOTE" = "Q" ]; then
        echo "Au revoir."
        exit 0
    fi

    # verif que la saisie est un nombre
    if ! [[ "$NOTE" =~ ^[0-9]+([.][0-9]+)?$ ]]; then
        echo "Erreur : veuillez entrer un nombre ou q."
        continue
    fi

    # affiche l'appreciation
    if [ $(echo "$NOTE >= 16" | bc) -eq 1 ] && [ $(echo "$NOTE <= 20" | bc) -eq 1 ]; then
        echo "Très bien"
    elif [ $(echo "$NOTE >= 14" | bc) -eq 1 ]; then
        echo "Bien"
    elif [ $(echo "$NOTE >= 12" | bc) -eq 1 ]; then
        echo "Assez bien"
    elif [ $(echo "$NOTE >= 10" | bc) -eq 1 ]; then
        echo "Moyen"
    else
        echo "Insuffisant"
    fi
done
```