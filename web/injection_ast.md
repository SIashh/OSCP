# Injection AST (ou prototype pollution)

> Tips pour l'exploitation d'injections AST
>
> Attaque permettant notamment de RCE via du Javascript

**Prérequis :**

- Moteur de template : `pug, handlebars`
- Fonction `unflatten`
- Une fonction permettant de set des propriétés d'un object (le prototype d'Object car tous les objets héritent de ses propriétés)



### Ressources

- https://blog.p6.is/AST-Injection/
- https://book.hacktricks.xyz/pentesting-web/deserialization/nodejs-proto-prototype-pollution
- https://research.securitum.com/prototype-pollution-rce-kibana-cve-2019-7609/