# Msfconsole

> Commande de la console MSF de Metasploit

- **Ouvrir une console MSF : **

  ```bash
  $ msfconsole (-q pour enlever les graphismes)
  ```



- **Chercher un exploit :**

  ```bash
  msf6> search <COMPOSANT>
  # Exemple : search apache
  ```



- **Utiliser un exploit :**

  ```bash
  # Sélectionner un exploit
  msf6> use <exploit>
  # Lister les paramètres nécessaires à définir pour utiliser l'exploit
  msf6> options
  # Définir un paramètre nécessaire à l'exploit
  msf6> set <VARIABLE> <VALEUR>
  MSF6> run
  ```

  

