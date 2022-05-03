# Utilitaires

> Tips et tricks pour de l'exploitation / recherche de vulnérabilités statique sur android.



### 1. Backup android

> Restaurer les fichiers d'une backup android (`.ab`).
>
> Source : https://infosecwriteups.com/extract-an-android-backup-file-96172efd4d86

```bash
# On rajoute le header gzip
$ (printf "\x1f\x8b\x08\x00\x00\x00\x00\x00" ; tail -n +5 <FICHIER>.ab) | tar -xvz
```



### 2. Modifier un apk

> Modifier le contenu d'un apk.
>
> Marche s'il n'y a pas de vérification de signature.

```bash
$ apktool d <APK>.apk
# Modifier les fichiers obtenus
$ apktool b <NOM_APK_MODIFIE>
# Signature
$ keytool -genkey -v -keystore my-release-key.keystore -alias alias_slash -keyalg RSA -keysize 2048 -validity 10000
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore <NOM_APK_MODIFIE.APK> alias_slash
```

