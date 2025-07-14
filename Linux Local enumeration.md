
# Linux Local Enumeration
dopo aver ottunuto accesso ad un sistema linux, é importante ottenere informazioni sul sitema in esecuzione. <br> 
Quello che andremo a cercare sono gli hostnames, il tipo di distribuzione del sistema operativo, la verione del kernel, informazioni sulla CPU, indoramzioni sullo storage e informazioni sui programmi in esecuzione <br> 
```bash
hostname
cat /etc/issue           # per vedere la distribuzione
cat /etc/*release        # mostra altre informazioni sulla distribuzione
uname -r                 # mostra la versione del kernel
env                      # mostra le variabili d'ambiente
lscpu                    # mostra informazioni della CPU
free -h                  # per vedere lo storage
df -h                    # per visualizzare le unitá e i dischi del sistema  
dpkg -l                  # per visualizzare i software installati 
```

### Enumerare utenti e gruppi 
Durante un pentration test é importante definire gli utenti e i loro gruppi presenti all'interno della macchina target. <br> 
```bash
meterpreter > getuid
meterpreter > shell
whoami
groups                                    # mostra tutti i gruppi presenti nel sistema target
groups <USERNAME>                         # per vedere a quale gruppo appartiene quel utente
cat /etc/passwd                           # é una lista che mostra tutti gli utenti che hanno una password. Sono presenti anche degtli utenti che fanno riferimento ai servizi in esecuzione sul target
cat /etc/passwd | grep -v /nologin        # per mostrare solo gli utenti e non gli account dei servizi in esecuzione
last                                      # mostra l'ultimo utente che si é connesso alla macchina target
lastlog                                  # mostra tutte le ultime volte che gli utenti hanno effettuato l'accesso alla macchina target
```

### Enumerare le impostazioni network
```bash
meterpreter > ifconfig
meterpreter > netstat   	                            # per visualizzare i servizi TCP/UDP in esecuzione
meterpreter > route                                   # per visualizzare la routing table
meterpreter > arp 
meterpreter > shell
cat /etc/network
cat /etc/hostname
cat /etc/hosts
cat /etc/resolv.conf
arp -a 
```

### Enumerazione dei processi e di CronJobs
é molto importante trovare quali servizi sono in eseczuzione nella macchina target e se sono presenti degli script cronjob da poter sfruttare
```bash
meterpreter > ps
meterpreter > pgrep <PROCESSO>
meterpreter > shell
ps
ps aux | grep <PROCESSO/USER> 
top
crontab -l                         # per mostrare i cronjobs
ls -la /etc/cron*
cat /etc/cron*
```

### Automatizzare l'enumerazione locale
per poter automatizzare l'enumerazione locale di un target esiste un tool chiamato LinEnum disponibile a questo <a href="https://github.com/rebootuser/LinEnum">link</a>. <br>
Vediamo prima dei moduli di post exploitation su linux
```bash
use post/linux/gather/enum_configs
set SESSION 1
run
use post/linux/gather/enum_network
set SESSION 1
run
use post/linux/gather/enum_network
set SESSION 1
run
use post/linux/gather/checkvm
set SESSION 1
run
```
ora utiliziamo il tool LinEnum. come primo passo dobbiamo copiare il file .sg per poi copiarlo nel nostro sistema kali linux in modo da poterlo uploadare sul target e farlo partire
```bash
meterpreter > upload LinEnum.sh
meterpreter > shell

```
