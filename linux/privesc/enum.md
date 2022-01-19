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
  $ find / -type f -readable | grep -v "/sys" | grep -v "/proc" | grep -v "/usr" | grep -v "sbin" | grep -v "/lib" | grep -v "/boot" | grep -v "/etc/" | grep -v "/run" | grep -v "/bin"
  ```

  

### 2. Utilisation de scripts

- **Linpeas**