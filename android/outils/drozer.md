# Drozer

> Framework client /server automatisé de sécurité Android.
>
> Permet de :
>
> - analyser une APK (lancer des scans de vulnérabilités, e.g. SQLi)
> - déclencher des actions (intents, broadcast, services)



### 1. Configuration

> https://oncybersec.com/accessing-android-service-using-adb/

* Sur le téléphone émulé, installer puis ouvrir l'application installée pour lancer le server drozer : 

```bash
# https://github.com/FSecureLABS/drozer/releases/tag/2.3.4/drozer-agent-2.3.4.apk 
$ adb install drozer-agent-2.3.4.apk # ou drag&drop dans le mobile émulé
```

- Sur la machine hôte :

```bash
# Fermer genymotion et kill les process adb
# Relancer le server adb pour qu'il écoute sur toutes les interfaces
$ adb -a nodaemon server start
# Démarrer Genymotion et démarrer le mobile virtuel
# Faire le port forward (pour docker), ici, le port hôte 1234 redirige vers le port 31415 du téléphone émulé
$ adb forward tcp:1234 tcp:31415
# Lancer le docker contenant drozer
$ sudo docker run -it fsecurelabs/drozer 
# Dans le docker
root@05e73b2dbd82:/# drozer console connect --server <IP_HOTE>:1234
# On a notre console drozer
dz>
```



### 2. Commandes

- **Lister les packages :**

```bash
dz> run app.package.list -f <NOM_APPLI>
```

- **Vérifier qu'un broadcast receiver est bien exporté (et donc accessible par tous) et les permissions nécessaires pour les trigger :**

```bash
# Exemple : run app.broadcast.info -a infosecadventures.allsafe
# Package: infosecadventures.allsafe
# infosecadventures.allsafe.challenges.NoteReceiver
#   Intent Filter:
#     Actions:
#       - infosecadventures.allsafe.action.PROCESS_NOTE
#   Permission: null

dz> run app.broadcast.info -a <PACKAGE_de_base> -i
```

