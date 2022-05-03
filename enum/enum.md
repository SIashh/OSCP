# Enumeration

> Différentes techniques d'énumération à réaliser (sans accès)
>



## 1. Réseau

- **Liste des ports** : voir [nmap](./nmap.md)

- **Liste des services réseaux d'une machine compromise :**

  ```bash
  $ netstat -tulnap
  ```

  


## 2. Web

- **Bruteforce de répertoires / fichiers** : voir [dirsearch](../web/outils/dirsearch.md)
- **Bruteforce de VHOST** : voir [gobuster](../web/outils/gobuster.md)
- **Bruteforce des paramètres GET / POST / etc.** : utiliser paraminer et bien spécifier un canary réfléchi (e.g. /etc/passwd si on recherche une LFI)

- **Spidering** 
- **Regarder si des différences entre IP et FQDN existent** 
