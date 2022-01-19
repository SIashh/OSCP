# Dirsearch

> Tips pour l'utilisation de dirsearch

- **Scan basique :**

```bash
$ python3 ~/tools/linux/enum/dirsearch/dirsearch.py -u "<URL>" -x 404 --random-agent
```

* **Scan personnalisé** (beaucoup d'extensions) : 

```bash
$ python3 ~/tools/linux/enum/dirsearch/dirsearch.py -u "<URL>" -x 404 --random-agent -e asp,aspx,backup,bak,conf,config,db,jsp,old,orig,php,py,sql,swp,tar,tgz,txt,xml,zip
```



### Options

- `-q` : Pas de verbosité 
- `--random-agent ` : Change le user-agent à chaque requête
- `-x <code>` : Exclure des réponses selon les codes de réponse HTTP `<code>`
- `--exclude-sizes=sizes` : Exclure des réponses selon la taille `<sizes>` de la réponse HTTP  
- `-e <extension>` : Les extensions balisées dans la wordlist de l'outil sont changées par `<extension>` 
- `-f <extension>` : Toutes les extensions des fichiers de la wordlist sont forcées à être `<extension>`

- `-w <wordlist>` : Une wordlist
