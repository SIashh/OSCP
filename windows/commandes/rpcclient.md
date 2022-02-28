# RPCCLIENT

> Tips et commandes pour utiliser rpcclient, un outil d'énumération au travers du protocole RPC
>
> Prérequis : une connexion établie auparavant

- **Connexion via le protocole RPC :**

```bash
# Accès anonyme
$ rpcclient -U "" -N <IP>

# Accès avec identifiants
$ rpcclient -U "<UTILISATEUR>" <IP>
```



- **Récupération d'informations une fois la connexion établie :**

```bash
# récupération d'informations à propos du DC
rpc> srvinfo

# récupération de la politique de mots de passe
rpc> getdompwinfo

# récupération des groupes du domaine 
rpc> enumdomains
rpc> enumdomgroups
rpc> enumalsgroups builtin

```

