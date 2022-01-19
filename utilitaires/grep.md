# Grep

> Commandes utiles pour rechercher dans des fichiers

- **Rechercher récursivement une chaine et faire apparaitre le contexte autour (*ici, 10 lignes avant et après*) :**

```bash
$ grep -Ria -C 10 "<CHAINE>"
```

- `-R` : récursif
- `-i` : insensible à la casse
- `-a` : également dans les fichiers binaires
- `-C <nombre>` : afficher <nombre> de lignes avant et après chaque chaine trouvée



* **Rechercher récursivement une chaine et faire apparaitre un contexte précis autour (*ici, 100 caractères après la chaine*) :**

```bash
$ grep -Ria -o -P "chaine.{0,100}"
```

- `-o` : ne pas matcher les résultats vides
- `-P <REGEX>` : utiliser une REGEX



- **Rechercher des clés privées PGP :**

```bash
$ grep -Ria "BEGIN PGP PRIVATE KEY"
```



- **Rechercher des clés privées SSH :**

```bash
$ grep -Ria "BEGIN OPENSSH PRIVATE KEY"
```

