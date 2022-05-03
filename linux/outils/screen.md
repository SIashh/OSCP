# Screen

> Privesc root avec la commande screen

```bash
# Le binaire nécessite d'être setuid
$ screen -r root/root
```



### Options

- `-r <propriétaire de la session>/<session>` : 

  ```txt
  -r sessionowner/[pid.tty.host]
  resumes  a  detached  screen session. No other options (except combinations with -d/-D) may be specified, though an optional prefix of [pid.]tty.host may be needed to distinguish between multiple detached screen sessions. The second form is used to connect to another user's screen session which runs in multiuser mode. This indicates that screen should look for sessions in another user's directory. This requires setuid-root.
  ```

  