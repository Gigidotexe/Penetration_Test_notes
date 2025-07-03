
# Armitage
Armitage é la versione free Java based **GUI di Metasploit Framework** che offre altre funzionalitá come la visualizzazione dei targets, automatizzare le scansioni delle porte, autimatizzare gli exploit e automatizzare i post exploit. <br>
Armitage ha bisogno del database e del servizio backend di Metasploit Framework per poter funzionare ed é preinstallata su kali linux. <br>
Abbiamo anche bisogno che Metasploit sia in esecuzione per poter usare Armitage
```bash
service postgresql start && msfconsole
db_status
```
in un altro terminale scriviamo 
```bash
armitage
```
oppure cerchiamo `armitage` tra le applicazioni di kali linux. <br>
Ci verrà successivamente chiesto di innserire alcune informazioni come LHOST, una porta, un nome utente e una password (possiamo lasciare tutte informazioni di default e cliccare su `connect`. <br>
Successivamente avviamo il server premendo `yes` nella finestra successiva e dopo un breve caricamento la GUI di Armitage verrá caricata. <br>

Nella sezione di sinistra avremo tutti i moduli che possiamo usare con una casella dove cercare uno specifico modulo, nella parte inferiore avremo una console standar di Metasploit Framework e nella parte superiore avremo una sezione grafica dove verranno mostrati i targets. <br>
Nel menù in alto selezioniamo `Armitage`, `set target view` e `graph view`. <br>
Possiamo anche esportare i dati per poterli usare in un report selezionando `view`, `reporting` e infine `export data` nel menù in alto. <br>

Per poter aggiungere un host da scansionare clicchiamo `hosts` e `add hosts` nel menù in alto per poi assegnargli un nome cliccando col tasto destro del mouse sull'icona del host appena inserito e poi su `hosts` e `set label`. <br>
Successivmaente possiamo effettuare una scansione delle porte cliccando col tasto destro del mouse sull'icona del host e poi su `scan` per poi, appena la scansione sará terminata, possiamo cliccare di nuovo col tasto destro sull'icona e poi `services` per visualizzare i servizi in esecuzione sulla macchina target. 

Per effettuare una scansione con nmap possiamo cliccare su `hosts` nel menu in alto e poi `nmap scan` e selezionare la scansione che vogliamo effetutare sul target. 

---

Dopo aver effettuato una scansione, possiamo cercare un exploit nel menù di sinistra e se l'exploit dovesse avere successo, l'icona dell host cambiará diventando rossa, questo significa che quella macchina 
é stata exploitata. <br>
Possiamo successivamente aprire uan console `meterpeter` cliccando col tasto destro sull'icona della macchina e poi `meterpeter`. <br>
possiamo poi affettuare molte operazioni di post exploitation come il dumping degli hash, cliccando sull'icona del target exploitato poi `meterpeter`, `access`, `dump hashes` e `lsass method`. <br>
Possiamo poi visualizzare il loot cliccando nel menù in alto `view` e poi `loot`. <br>

Possiamo poi visualiizzare le informazioni del target come directory, processi, i log della tastiera o fare uno screenshot tramite la finestra `explore` nel menú di `meterpeter`. <br>

É molto importante la parte del pivoting che é possibile impostare cliccando col tasto destro sul target, poi `meterpreter`, `pivoting` e poi `setup`. <br>

