# Hashcat

> Tips pour craquer un hash à l'aide d'hashcat

## Cracking

- Crack un hash avec une wordlist :

  ```bash
  $ hashcat -m <n°_type_hash> -a 0 hash.hash <WORDLIST>
  ```

- Crack un hash Net-NTLMv2 :

  ```bash
  $ hashcat -m 5600 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
  ```

- Crack un TGS :

  ```bash
  $ hashcat -m 13100 -a 0 tgs.hash /usr/share/wordlists/rockyou.txt --force
  ```
