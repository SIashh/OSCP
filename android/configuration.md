# Android - Analyse dynamique

> Tips et tricks pour de l'exploitation / recherche de vulnérabilités sur android de manière dynamique.



### 0. Configuration

> Installation et configuration pour analyser une application Android.
>
> Ressources : 
>
> - https://blog.yeswehack.com/yeswerhackers/bug-bounty-android-setup-genymotion-environment-apk-analysis/
> - https://arben.sh/bugbounty/Configuring-Frida-with-Burp-and-GenyMotion-to-bypass-SSL-Pinning/

**Sur le téléphone émulé, à installer (drag&drop ou `apk install`) :**

- Dans tous les cas (problèmes de traduction ARM) : https://github.com/m9rco/Genymotion_ARM_Translation

- Pour [Drozer](outils/drozer.md) : https://github.com/FSecureLABS/drozer/releases/tag/2.3.4/drozer-agent-2.3.4.apk
- Pour utiliser un proxy : "Wifi" > "Paramètres" > "Avancé" > "Proxy manuel" > Mettre l'IP Vbox (interface entre l'hôte et le téléphone émulé)

- Pour utiliser un proxy (bypass SSL pinning) : https://github.com/frida/frida/releases *(frida-server)*

**Sur Genymotion, configurer :**

- Paramètres > Réseau > Utiliser un proxy HTTP > 127.0.0.1:8080

**Sur l'hôte : **

- Pour [Frida](outils/frida.md) : Créer le fichier `adbhelper.sh` présent dans la section `X.Ressources`
- Pour Drozer, pull le docker : https://hub.docker.com/r/fsecurelabs/drozer



### 2. Genymotion / frida

> Emulateur android et outil permettant de récupérer le trafic https d'une application.
>
> Genymotion embarque l'outil CLI `adb` (permet d'intéragir avec le mobile émulé, par exemple, y déposer des fichiers ou exécuter des commandes dessus), il faut donc trouver son répertoire d'installation (nous, `~/genymotion/tools`).
>
> Dans les applications mobiles modernes, une protection contre le MITM est mise en place, c'est le **SSL pinning**. Le principe est simple : l'application est forcée de re-vérifier le certificat du serveur avec une liste de certificat embarquée (épinglée) dans cette dernière au moment du développement.
>
> Installation : 
>
> - https://blog.yeswehack.com/yeswerhackers/bug-bounty-android-setup-genymotion-environment-apk-analysis/
> - https://arben.sh/bugbounty/Configuring-Frida-with-Burp-and-GenyMotion-to-bypass-SSL-Pinning/
>
> A configurer : 
>
> - Burp
> - Mobile émulé
> - Genymotion

```bash
$ cd ~/genymotion/tools

# On lance le server frida sur le téléphone en background
$ adb shell /data/local/tmp/frida-server &

# On utilise le petit script adbhelper.sh pour bypass le SSL pinning
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
# Et voilà, on peut récupérer le trafic de l'application
```

```bash
# vérifier que Frida marche
# Afficher les processus et les packages tournants sur le mobile
$ frida-ps -U 
```





#### X. Ressources

```bash
# adbhelper.sh (permet de bypass le SSL pinning)

#!/bin/bash

# ====================================
# Small script to:
#  1. Run Frida server and hook the script to bypass SSL pinning.
#  2. Pull an APK from the emulated device in Genymotion.
# ====================================

PS3='[+] Please select a number from menu: '
options=("Hook with Frida" "Pull an APK" "Quit")
select opt in "${options[@]}"
do
        echo -e "\n"
    case $opt in
        "Hook with Frida")

                        read -p "[+] Enter keyword of your app: " keyword

                        OUTPUT=$(adb shell pm list packages | grep $keyword)
                        if [[ "$OUTPUT" ]]
                        then
                                oldIFS=$IFS
                                IFS=$'\n'
                                choices=( $OUTPUT )
                                IFS=$oldIFS

                                PS3="[+] Please select the package number: "
                                select answer in "${choices[@]}"; do

                                  for item in "${choices[@]}"; do
                                    if [[ $item == $answer ]]; then
                                        app=$(echo "$answer" | sed -e 's/\(^package:\)//g')
                                                adb shell /data/local/tmp/frida-server &
                                                echo -e "\n================================================"
                                                echo -e "\t\tFrida Started!"
                                                echo "================================================"
                                                frida -U --codeshare sowdust/universal-android-ssl-pinning-bypass-2 -f "$app" --no-pause
                                                f_pid=$(adb shell ps | grep frida-server | grep poll_schedule_timeout | awk ' { print $2 }')
                                                kill_frida=$(adb shell kill -9 "$f_pid")
                                                exit

                                      break 2
                                    fi
                                    
                                  done
                                done 
                        else
                                echo -e "\nNo APK found with this keyword: $keyword"

                        fi
            ;;

        "Pull an APK")

            read -p "[+] Enter keyword of your app to pull: " keyword

                        OUTPUT=$(adb shell pm list packages | grep $keyword)
                        if [[ "$OUTPUT" ]]
                        then
                                oldIFS=$IFS
                                IFS=$'\n'
                                choices=( $OUTPUT )
                                IFS=$oldIFS
                                PS3="[+] Please select the number to pull: "
                                select answer in "${choices[@]}"; do
                                  for item in "${choices[@]}"; do
                                    if [[ $item == $answer ]]; then
                                        app=$(echo "$answer" | sed -e 's/\(^package:\)//g')
                                                path=$(adb shell pm path "$app" | grep base.apk | sed -e 's/\(^package:\)//g') 
                                                rename=$( echo "$path" | sed -e 's/\(^\/data\/app\/\)//g' -e 's/-.*$//' -e 's/\./_/g')
                                                saved=$(adb pull "$path" "$rename.apk")
                                                echo -e "\n"
                                                echo "[+] $rename.apk has been saved!"
                                                exit

                                      break 2
                                    fi
                                    
                                  done
                                done
                        else
                                echo -e "\nNo APK found with this keyword: $keyword"

                        fi
            ;;
       
        "Quit")
            break
            ;;
        *) echo "[-] Invalid option: $REPLY";;
    esac
done
        
```

