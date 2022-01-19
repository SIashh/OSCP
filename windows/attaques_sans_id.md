# Attaques Windows sans identifiants

### 1. RPC anonyme

> Connexion anonyme à l'annuaire de l'AD.
>
> Objectifs : par exemple, consulter la liste des utilisateurs du domaine.

``` bash
$ rpcclient -p <PORT> -U "" -N <IP>
```

Voir [rpcclient](./rpcclient.md) pour consulter les actions réalisables.

### 2. LLMNR/NBT-NS

> Attaque Man-In-The-Middle, consistant à intercepter se faire passer pour la ressource demandée afin que la machine s'authentifie sur nous.
>
> LLMNR : Protocole de résolution de nom, utilisé pour chercher un FQDN sur le réseau afin de s'y connecter. 
>
> Ordre de recherche DNS par la machine : base locale (/etc/hosts) > LLMNR > serveur DNS (/etc/resolv.conf).
>
> Objectifs : récupérer le hash Net-NTLMv2 pour le craquer et obtenir le mot de passe de l'utilisateur (pas de pass-the-hash).

```bash
$ sudo responder -I eth0 -wFv
```

### 3. Accès anonyme SMB

> Accéder à un share SMB sans idenfiants

```bash
$ smbclient -L '\\IP' -U ''%''

$ crackmapexec smb <IP> -u '' -p '' --shares
```

