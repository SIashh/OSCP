# Symfony

> Petits tips pour l'audit de code / TI.

### 1. XSS

- Symfony utilise de base le moteur de template **twig** pour afficher les vues ;

- **Twig** échappe de base les caractères à l'affichage (paramètre de configuration `autoescape`) ;

- Il est possible d'utiliser le filtre `raw` qui désactive l'échappement et donc potentiellement XSS : `{{ INPUT|raw }}`.

**Référence** : https://symfony.com/doc/current/reference/configuration/twig.html#autoescape



### 2. Injection SQL

- Symfony utilise **Doctrine** ;

- Certaines API sont safes, d'autres non :

  > The ORM is much better at protecting against SQL injection than the DBAL alone. You can consider the following APIs to be safe from SQL injection:
  >
  > - `\Doctrine\ORM\EntityManager#find()` and `getReference()`.
  >
  > - All values on Objects inserted and updated through `Doctrine\ORM\EntityManager#persist()`
  >
  > - All find methods on `Doctrine\ORM\EntityRepository`.
  >
  > - User Input set to DQL Queries or QueryBuilder methods through
  >
  > - - `setParameter()` or variants
  >   - `setMaxResults()`
  >   - `setFirstResult()`
  >
  > - Queries through the Criteria API on `Doctrine\ORM\PersistentCollection` and  `Doctrine\ORM\EntityRepository`.
  >
  > You are **NOT** safe from SQL injection when using user input with:
  >
  > - Expression API of `Doctrine\ORM\QueryBuilder`
  > - Concatenating user input into DQL SELECT, UPDATE or DELETE statements or  Native SQL.
  >
  > This means SQL injections can only occur with Doctrine ORM when working with Query Objects of any kind. The safe rule is to always use prepared statement parameters for user objects when using a Query object.

**Référence** : https://www.doctrine-project.org/projects/doctrine-orm/en/2.9/reference/security.html



### 3. CSRF

- Protection pouvant être facilement implémentée ;
- Elle met en place un champ caché dans les formulaires `_token` ;
- Elle se configure dans le fichier `/config/packages/framework.yaml` au travers du paramètre `csrf_protection` ;
- Elle s'applique sur les formulaires créés avec le `$builder` de Symfony (interface `AbstractType`) ;
- Les formulaires dans `/Form` utilisent normalement le builder et sont donc non vulnérables aux CSRF.

**Référence** : https://symfony.com/doc/current/security/csrf.html



### 4. Gestion des droits d'accès

Il existe plusieurs façons de gérer les droits d'accès dans Symfony :

- Via le contrôle d'accès intégré par défaut dans le fichier `/config/packages/security.yaml` ;

- Via le framework `SensioFrameworkExtraBundle` qui permet l'utilisation d'annotations, e.g. :

  ```php
  /**
   * @IsGranted("ROLE_INSTRUCTEUR")
   */
  ```



### 5. SSTI

- Symfony utilise de base le moteur de template **twig** pour afficher les vues ;

- Vulnérable dans le cas où une expression de template inclu des entrées utilisateurs non filtrées : 

  ```php
  # Vulnérable
  $output = $twig > render ('expression_de_template'.$_GET(USER_INPUT), $data)
  # Non vulnérable 
  $output = $twig > render (template.twig, $data.$_GET(USER_INPUT))
  ```





## Ressources

- [Excellent blog pour la sécurisation d'un Symfony][0]

- [Outil permettant la vérification des dépendances d'un composer][1]



## Lien

[0]: https://nicwortel.nl/blog/2020/06/07/protect-symfony-application-against-owasp-top-10-security-risks
[1]: https://github.com/fabpot/local-php-security-checker