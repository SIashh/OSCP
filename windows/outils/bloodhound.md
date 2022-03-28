# Bloodhound

> Utilisation et tricks sur Bloodhound.
>
> Outil servant à visualiser les données présentes dans un AD notamment.
>
> Prérequis : un compte (local ou du domaine) sur un poste relié au domaine



### 1. Lancer bloodhound

```bash
$ service neo4j start
$ /usr/bin/bloodhound
```



### 2. Récolter les données nécessaires à bloodhound (ingestors) 

#### 2.1. Sharphound : 

> Prérequis : identifiants locaux ou du domaine d'un poste relié à l'AD + exécution de code
>
> Inconvéniants :  l'exécutable doit être déposé sur la machine cible  et nécessite la possibilité d'exécuter du code

```bash
# Génère un fichier à donner en entrée à BloodHound
# https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors
C> SharpHound.exe --outputdirectory C:\ --CollectionMethod All
```



#### 2.2. Bloodhound-python (bloodhound.py)

> Prérequis : identifiants d'un compte du domaine
>
> Inconvéniants : nécessité d'un compte du domaine et peut potentiellement être bloqué comme c'est à distance (firewall, ...)
>
> Avantages : à distance

```bash
# Toutes les méthpdes de collection (-c all)
$ bloodhound-python -d <DOMAIN.COM> -u '<USER_VALIDE>' -p '<MDP_VALIDE>' -gc <NOM_DC> -c all -ns <IP_DNS>
```



### 3. Interprêtation et requêtes

Les requêtes suivantes sont intéressantes :

- `Find Shortest Paths to Domain Admins` : self-explaining ;
- `List all Kerberoastable Accounts` : lister les comptes de services. 



Les labels et leur signification sont les suivants :

- `MemberOf` : l'utilisateur est membre du groupe ;
- `GenericAll` : l'utilisateur à tous les droits sur l'entité qui suit ;
- `ReadGMSAPassword` : l'utilisateur à le droit de lire les mots de passe des comptes GMSA.



### 4. Requêtes custom

- Rechercher les OS obsolètes : 

```bash
MATCH (H:Computer) WHERE H.operatingsystem=~'(?i).*(2000|2003|2008|xp|vista|7|me).*'RETURN H
```

