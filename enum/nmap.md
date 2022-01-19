# Nmap

> Tips pour l'utilisation de nmap

- **Scan rapide** (scripts - versionning - rapide - 1000 ports les plus communs) :

```bash
$ nmap -sC -sV -T4 <IP>
```

- **Scan de tous les ports** (idem que le scan rapide) : 

```bash
$ nmap -sC -sV -T4 -p- <IP>
```

- **Vérifier si les cibles sont up** (très rapide - ping / ARP / port 80) :

```bash
$ nmap -sn <IP/range>
```

- **Scan UDP** :

```bash
$ nmap -sU <IP>
```

- **Scan TCP** : 

```bash
$ nmap -sT <IP>
```



### Options

- `-Pn` : Continue de scanner la cible même si elle ne répond pas au ping
- `-sC` : Exécuter des scripts (équivalent de `--script=safe,intrusiv`)
- `-sV`:  Chercher les versions
- `--top-ports X`: Scanner les X ports les plus répandus