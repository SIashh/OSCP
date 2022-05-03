# Enum

> Ce qu'il faut énumérer pour réaliser une élévation de privilèges

### 1. File listing

- **Connaitre les fichiers qu'un utilisateur possède** :

  ```bash
  $ find / -type f -user $(whoami) 2>/dev/null | grep -v "/sys" | grep -v "/proc/"
  ```

- **Connaître les fichiers setuid** :

  ```bash
  $ find / -user root -perm -4000 -print 2>/dev/null
  ```

- **Connaître les fichiers qu'un utilisateur à le droit de consulter** :

  ```bash
  find / -type f -readable 2> /dev/null | grep -v "/sys" | grep -v "/proc" | grep -v "/usr" | grep -v "sbin" | grep -v "/lib" | grep -v "/boot" | grep -v "/etc/" | grep -v "/run" | grep -v "/bin" | grep -v ".css" | grep -v ".png" | grep -v ".gif" | grep -v ".js" | grep -v ".wav" | grep -v ".woff2" | grep -v ".ttf" | grep -v "jpg" | grep -v "ico"
  ```

  

### 2. Utilisation de scripts

- **Linpeas** : https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS
- **LES** : https://github.com/mzet-/linux-exploit-suggester