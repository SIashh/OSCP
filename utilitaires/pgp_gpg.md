# PGP / GPG

> Déchiffrer des messages PGP / GPG avec une clé privée

```bash
# Récupérer la passphrase de la clé privée
$ gpg2john priv.key > gpghash.tmp 
$ cat gpghash.tmp | grep -E -o '\$gpg\$[^:]+' > gpg.hash 
$ john --wordlist=~/tools/rockyou.txt --format=gpg gpg.hash 

# Déchiffrer un message chiffré
$ gpg --import priv.key
$ gpg --decrypt message.gpg       
```

