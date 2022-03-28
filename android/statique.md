# Android

> Tips et tricks pour de l'exploitation / recherche de vulnérabilités statique sur android.



### 1. Backup android

> Restaurer les fichiers d'une backup android (`.ab`).
>
> Source : https://infosecwriteups.com/extract-an-android-backup-file-96172efd4d86

```bash
# On rajoute le header gzip
$ (printf "\x1f\x8b\x08\x00\x00\x00\x00\x00" ; tail -n +5 <FICHIER>.ab) | tar -xvz
```

