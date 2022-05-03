# Docker

> Commandes dockers utiles pour démarrer rapidement un environnement et l'explorer

* **Lancement d'une image :**

  ```bash
  # Chargement du tar
  $ sudo docker load -i image.tar
  
  # Listing des images contenu dans le tar
  $ sudo docker images
  
  # Chargement d'une des images contenu dans le tar
  $ sudo docker run --publish <PORT_IMAGE>:<PORT_HOTE> <NOM_IMAGE>:latest
  
  # Accès à l'application
  $ wget http://localhost:<PORT_HOTE>
  ```

- **Obtention d'un shell dans une image :**

  ```bash
  # Listing des containers
  $ sudo docker ps
  d2d4a89aaee9  larsks/mini-httpd  "mini_httpd -d /cont  7 days ago  Up 7 days  web
  
  # Exécution d'un shell bash
  $ docker exec -it <NOM_OU_ID_CONTAINER (première et dernière colonne)> bash
  ```