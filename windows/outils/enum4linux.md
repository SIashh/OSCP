# Enum4linux

> Tips d'utilisation pour l'outil enum4linux.
>
> Outil permettant d'énumérer un système à distance si l'on possède des identifiants d'un utilisateur du domaine ou si le null bind est autorisé.

Parmi les informations intéressantes à récupérer sur cet outil on peut noter : 

- la politique de mot de passe (utile pour savoir si on peut bruteforce) ;
- les commentaires laissés lors de la création des utilisateurs (peut contenir des mots de passe par exemple) ;
- la liste des utilisateurs ;
- la liste des shares SMB ;
- la liste des groupes.

```bash
# Tout énumérer avec un accès valide
$ enum4linux  -u <USER_VALIDE> -p <PASSWORD_VALIDE> -a <IP_DC>
```

