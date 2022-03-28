# Goddi

> Outil d'énumération de DC (comme enum4linux).
>



**Sur Windows** (donc accès local à la machine, sans AV) :

```powershell
# Télécharger dans un premier temps le binaire sur la machine cible
# -unsafe : forcer à utiliser le port 389 du LDAP (port non sécurisé)
.\goddi-windows-amd64.exe -username="<USERNAME>" -password="<MOT_DE_PASSE>" -domain="<DOMAINE>.<DOMAINE>" -dc="acute.local" -unsafe
```



**Sur Linux** (donc distant) :

```bash
$ ./goddi-linux-amd64 -username="<USERNAME>" -password="<MOT_DE_PASSE>" -domain="<DOMAINE>.<DOMAINE>" -dc="acute.local" -unsafe
```

