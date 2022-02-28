# Box

![](img/pic.png)

* IP :
* Difficulté :
* OS :



## 1. Foothold

### 1.1 Reconnaissance globale

#### 1.1.1 Nmap

```bash
$ nmap -sC -sV -T4 -p- IP

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



### 1.2 Reconnaissance Windows

Faire chacun des [tests sans identifiants](attaques_sans_id.md).



### 1.3 Analyse des éléments énumérés / exploitation





## 2. User privesc

### 2.1 Récolte d'informations



### 2.2 Exploitation

Faire chacun des [tests avec identifiants](attaques_avec_id.md).



## 3. Root privesc

### 3.1 Récolte d'informations



### 3.2 Exploitation

Faire chacun des [tests avec identifiants](attaques_avec_id.md).



## 4. Autres

#### 4.1 Composants / version

- [ ] Base de données : **todo** / **todo**
- [ ] Proxy : **todo** / **todo**
- [ ] WAF : **todo** / **todo**
- [ ] Framework global : **todo** / **todo**
- [ ] API (s) :  **todo** / **todo**
- [ ] Serveur web : **todo** / **todo**
- [ ] Language utilisé : **todo** / **todo**

### 4.2 Identifiants



### 4.3 Ressources

