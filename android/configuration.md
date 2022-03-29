# Android - Analyse dynamique

> Tips et tricks pour de l'exploitation / recherche de vulnérabilités sur android de manière dynamique.



### 0. Configuration

> Installation et configuration pour analyser une application Android.
>
> Outils nécessaires :
>
> - Genymotion (émulateur)
> - Frida
> - Burp
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

----------

**Sur Genymotion, configurer :**

- Paramètres > Réseau > Utiliser un proxy HTTP > 127.0.0.1:8080

---------

**Sur l'hôte : **

- Pour [Frida](outils/frida.md) : Créer le fichier `adbhelper.sh` présent dans la section `X.Ressources`
- Pour Drozer, pull le docker : https://hub.docker.com/r/fsecurelabs/drozer



#### 1. Ressources

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

