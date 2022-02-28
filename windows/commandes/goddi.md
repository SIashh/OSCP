# Goddi

> Outil d'énumération de DC (comme enum4linux).
>
> A exécuter en local sur la machine cible car seul un binaire compilé existe.

```powershell
# Télécharger dans un premier temps le binaire sur la machine cible
# -unsafe : forcer à utiliser le port 389 du LDAP (port non sécurisé)
.\goddi-windows-amd64.exe -username="<USERNAME>" -password="<MOT_DE_PASSE>" -domain="<DOMAINE>.<DOMAINE>" -dc="acute.local" -unsafe
```

