 # RCE

> Tips pour exploiter une RCE



- **Savoir si la cible pingback** (*RCE*) :

  ```bash
  $ sudo tcpdump -i <ethX> icmp and icmp[icmptype]=icmp-echo
  ```

- **Liste de syntaxes bash vulnérables aux injections de commande** :

  ```bash
  # https://stackoverflow.com/questions/56687976/what-are-the-possible-list-of-linux-bash-shell-injection-commands
  eval "grep -e \"$1\" /var/log/*"         
  eval "grep -e '$1' /var/log/*"           
  sh -c "grep -e \"$1\" /var/log/*"        
  sh -c "grep -e '$1' /var/log/*"          
  ssh somehost "grep -e \"$1\" /var/log/*" 
  ssh somehost "grep -e '$1' /var/log/*"   
  system("grep -e \"" + input + "\" /var/log/*")                 
  system("grep -e '" + input + "' /var/log/*")            
  subprocess.Popen('yourscript ' + arg, shell=True)
  xargs -I{} sh -c 'something_with {}'
  find . -type f -exec sh -c 'something_with {}' \;
  eval $1
  source $1
  ```
