# Adb

> Outil en CLI permettant d'intéragir avec un téléphone émulé (upload des fichiers, exécuter des commandes, ...)
>
> Logcat : 
>
> - https://developer.android.com/studio/command-line/logcat
> - https://developer.android.com/studio/command-line/logcat#filteringOutput

**Exécuter des commandes sur le téléphone émulé :**

```bash
# Simple exécution de commandes
$ cd ~/genymotion/tools
$ adb shell <COMMANDE>

# Ouvrir un shell sur le mobile
$ adb shell
```

**Rechercher dans les logs générés par les applications**

```bash
# Consulter tous les logs
$ adb logcat -v color

# Equivalent de grep case insentitive dans tous les logs 
$ adb logcat -e "(?i)<CHAINE_A_CHERCHER>" -v color

# Recherche dans une application en particulier
$ adb logcat -s <APPLICATION> -v color
```





