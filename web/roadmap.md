## 1. Foothold

### 1.1 Reconnaissance globale

#### 1.1.1 Nmap

```bash
$ sudo nmap -A -sC -T4 -p- IP

```

```bash
$ sudo nmap -sU IP

```

#### 1.1.2 Dirsearch

```bash
$ python3 /opt/dirsearch-git/dirsearch.py -u "http://FQDN" -e php,txt,go,json --random-agent -x 500,404,400

```

#### 1.1.3 Gobuster

```bash
$ gobuster vhost -u http://FQDN -w ~/tools/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -t 20 | grep 200

```

#### 1.1.4 Gospider

```bash
$ gospider -s http://domaine.com -d 5
```

