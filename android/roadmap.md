# Roadmap pentest Android

> Roadmap des tests à effectuer pour un pentest Android.



### 1. Analyse statique

> Prérequis : un fichier .apk



#### 1.1 Recherche d'informations sensibles codées en dur

```bash
Lancer Mobsf
- Onglet "Reconnaissance" > "Hardcoded Secrets"
- Onglet "Reconnaissance" > "URLs" (tokens dans les URLs par exemple)
```

```bash
# Décompiler l'application pour avoir plus d'infos / fichiers
$ apktool d <FICHIER.apk>
```





### 2. Analyse dynamique

> Prérequis : bien configurer les outils présents dans [la fiche dynamique.](dynamique.md)



#### 2.1 Récupération d'informations sensibles dans les logs

```bash
# Consulter tous les logs
$ adb logcat -v color

# Equivalent de grep case insentitive dans tous les logs 
$ adb logcat -e "(?i)<CHAINE_A_CHERCHER>" -v color

# Recherche dans une application en particulier
$ adb logcat -s <APPLICATION> -v color
```



#### 2.2 Bypass la détection root
