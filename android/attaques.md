# Attaques sur Android

> Différentes attaques / configuration à vérifier dans une apk.



#### 1. Broadcast receiver

> Prérequis : le fichier `AndroidManifest.xml` contient un `<receiver>` avec comme permissions `android:exported=true`
>
> N'importe quelle application est en mesure d'intéragir avec ce receiver et de récupérer la réponse.
>
> Ressources : 
>
> - https://hackerone.com/reports/289000
>
> - https://hackerone.com/reports/97295
>
> - https://oldbam.github.io/android/security/android-vulnerabilities-insecurebank-broadcast-receivers



#### 2. Bypass SSL pinning

> Contournement de la protection appelée "**SSL pinning**".
>
> Dans les applications mobiles modernes, une protection contre le MITM est mise en place, c'est le **SSL pinning**. Le principe est simple : l'application est forcée de re-vérifier le certificat du serveur avec une liste de certificat embarquée (épinglée) dans cette dernière au moment du développement.
>
> Pour contrer cette protection, on utilise [frida](outils/frida.md).

**Sur le téléphone émulé : **

- Installer le serveur frida :

  ```bash
  # Télécharger le serveur
  $ wget https://github.com/frida/frida/releases/download/<version-number>/frida-server-<version-number>-android-x86.xz
  # On dézipe
  unxz frida-server-<version-number>-android-x86.xz
  mv frida-server-<version-number>-x86.xz frida-server
  # On met le binaire sur le téléphone
  $ adb push ~/Downloads/frida-server /data/local/tmp
  ```

- Lancer le serveur en background :

  ```bash
  $ adb shell /data/local/tmp/frida-server &
  ```

**Sur l'hôte :**

- Lancer le script `adbhelper.sh` (voir [configuration.md](configuration.md)) :

  ```bash
  # Sélectionner l'option 1
  $ ./adbhelper.sh
  1) Hook with Frida
  2) Pull an APK
  3) Quit
  [+] Please select a number from menu: 1
  # On met un mot clé (e.g. le nom de l'apk) pour trouver le package associé
  [+] Enter keyword of your app: apk
  1) package:com.awesomeproject
  [+] Please select the package number: 1
  # Et voilà, on peut récupérer le trafic de l'application dans un proxy BURP
  ```