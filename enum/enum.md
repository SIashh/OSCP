# Enumeration



## Réseau

- Voir [nmap](./nmap.md)

- **Savoir si la cible pingback** (RCE) :

```bash
$ sudo tcpdump -i <ethX> icmp and icmp[icmptype]=icmp-echo
```



## Web

- **Bruteforce de répertoires / fichiers** : voir [dirsearch](./dirsearch.md)
- **Bruteforce de VHOST** : 

```bash
$ gobuster vhost -u <URL> -w ~/tools/wordlist/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -t 20
```

