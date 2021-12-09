# SMBCLIENT

- Récupérer un fichier présent sur un poste avec un partage SMB : 

  ```bash
  // Lister les partages
  # shares
  
  // Accéder à un partage
  # use <SHARE>
  
  // Lister les fichiers présents dans le partage sélectionné
  # ls
  
  // Récupérer un fichier
  # get <FICHIER_DISTANT>
  ```

- Mettre un fichier sur le poste (e.g. mimikatz, sharphound, etc.) :

  ```bash
  // Sélectionner un partage avant
  # put <FICHIER_LOCAL>
  ```