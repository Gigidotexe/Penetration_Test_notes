
# chkrootkit
É uno strumento open source per sistemi Unix/Linux che serve a rilevare la presenza di rootkit noti, controllando file di sistema, moduli kernel e configurazioni alla ricerca di segni di compromissione.<br>
Un **rootkit** è un tipo di malware progettato per ottenere e mantenere accesso privilegiato (root) su un sistema informatico, nascondendo la propria presenza agli utenti e ai software di sicurezza. <br>
Il suo scopo principale è permettere a un attaccante di controllare un computer compromesso senza essere scoperto, spesso alterando file di sistema, processi e log per rimanere invisibile.

Potremmo trovarci in una macchina con `chkrootkit` installato e magari esegue delle scansioni periodiche per potersi difendere da questo tipo di malware. <br>
possiamo usare il commando `ps aux` per vedere che programmi eseguono i processi e se qualcosa ci ricorda chkrootkit, possiamo copiare il path del codice eseguito e visualizzarlo con `cat` <br>
Se identifichiamo la sua presenza mentre ci troviamo con una shell aperta in una macchina, teniamo a mente il path dell'applicazione e possiamo verificare la sua versione usando il comando 
```bash
chkrootkit -V
```
per poi mettere la sessione in background e usare un modulo Metasploit per exploitare questa applicazione e ottenere degli alti privilegi
```bash
use exploit/unix/loca/chkrootkit
set CHKROOTKIT <pathDiChkrootkit>
set SESSION 1
run
```
`chkrootkit` per cercare tracce di rootkit, deve analizzare lo stato del sistema: file, processi, configurazione di rete e quindi probabilmente eseguirá automaticamente dei comandi come `ifconfig` o `netcat` per monitorare le impostazioni di rete. <br>
questo modulo crea un codice malevolo e lo chiama `ifconfig` o `netcat`, modifica poi la variabile di ambiente `PATH` che è una lista di directory, separate da :, che dice alla shell dove cercare gli eseguibili quando scrivi un comando, in modo che quando viene eseguito un comando `ifconfig` o `netca` esegua prima il codice malevolo creato e poi il reale `ifconfig` o `netcat`. <br>
successivametne si aspetta che il `chkrootkit` venga eseguito per poter eseguire il nostro codice malevolo con il nome di un comando che deve eseguire in modo da darci una shell sul target con permessi elevati. 
