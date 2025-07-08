# Powershell Empire
PowerShell Empire (chiamato anche “Empire”) è un framework di **post-exploitation** e **Command & Control** (C2) open source, originariamente sviluppato dal team di Veris Group (poi confluito in SpecterOps). <br>

Si basa pesantemente su PowerShell, sfruttandone le potenzialità di automazione e scripting integrate nei sistemi Windows, il che lo rende uno strumento particolarmente potente in ambienti Microsoft. <br>
Empire è infatti progettato per operare anche senza scaricare file su disco, tramite tecniche cosiddette **fileless**, utilizzando payload che vengono caricati ed eseguiti interamente in memoria riducendo drasticamente la possibilità di essere intercettato da antivirus tradizionali o controlli basati su firme di file. <br>

Empire si distingue inoltre per un’architettura flessibile e su comunicazioni crittograficamente sicure, consentendo di stabilire canali cifrati tra i sistemi compromessi (gli agent) e il server di comando e controllo. <br>
Un aspetto molto avanzato di Empire, rispetto a tanti altri strumenti, è la sua capacità di eseguire agent PowerShell anche **senza dover richiamare direttamente powershell.exe**, sfruttando quindi tecniche alternative per avviare codice PowerShell. <br>
Questo rende più difficile rilevare l’attività malevola tramite controlli sui processi in esecuzione. <br>

Il framework include un’ampia raccolta di moduli di post-exploitation che possono essere distribuiti rapidamente sui target, spaziando da **keylogger** a **Mimikatz**, e dispone di comunicazioni adattabili progettate proprio per evitare il rilevamento di rete, mantenendo un profilo basso e sfuggendo a monitoraggi di traffico sospetto. <br>
Il tutto è racchiuso in un framework che dà molta importanza alla facilità d’uso, con una console interattiva **simile a Metasploit**, comandi facilmente memorizzabili e gestione centralizzata delle sessioni. <br>

Il progetto originale è stato abbandonato nel 2019, ma è stato successivamente ripreso e mantenuto dalla comunità (BC-Security e altri) che continuano ad aggiornarlo, introducendo miglioramenti per l’evasione di AV e supporto moderno a Python3. <br>
Più recentemente, PowerShell Empire ha ricevuto un ulteriore rilancio grazie al team di **Kali Linux**, che **lo ha integrato ufficialmente nei propri repository**, garantendo aggiornamenti e pacchetti testati, accompagnati anche da **Starkiller**, un’interfaccia grafica web per gestire il framework in modo più intuitivo. <br>

Per maggiori dettagli, il team di Kali ha pubblicato un <a href="https://www.kali.org/blog/empire-starkiller/">approfondimento</a>.

---

## Starkiller
Starkiller è la GUI frontend ufficiale per PowerShell Empire. <br>
Si tratta di un’applicazione grafica progettata per semplificare e rendere più intuitiva l’interazione con Empire, che normalmente viene gestito tramite console testuale. <br>

Dal punto di vista tecnico, Starkiller è un’applicazione sviluppata in Electron e utilizza VueJS come framework frontend. <br>
Questo significa che funziona come un’app desktop multipiattaforma ma costruita con tecnologie web (HTML, CSS, JavaScript), fornendo così un’interfaccia moderna e accessibile. <br>

Starkiller è stato sviluppato da BC Security, la stessa azienda che attualmente mantiene il progetto Empire. <br>
Questo garantisce una perfetta integrazione, oltre a un aggiornamento coordinato tra il core engine di Empire e la sua interfaccia grafica. <br>

L’obiettivo principale di Starkiller è quello di offrire agli operatori di red team e ai penetration tester un modo più visuale e user-friendly per:
- gestire le sessioni attive,
- esplorare i moduli di post-exploitation disponibili,
- configurare listener e stager,
- monitorare e inviare comandi agli agent compromessi,
- osservare i risultati delle operazioni in modo ordinato.

---
Per poter installare e avviare Empire (e Starkiller), usiamo i comandi: 
```bash
sudo apt update
sudo apt install -y powershell-empire starkiller
sudo powershell-empire server                       # Per avviare il server
sudo powershell-empire client                       # Per avviare il client
```
per poter avviare starkiller basta cercare nel menú di kali `starkiller` ed effettuare l'accesso con le credenziali `empireadmin` e `password123`. <br>

Nel terminale principale sul Empire CLient (e le prime tre opzioni su Starkiller) possiamo mostrare i `listener`, gli `agents` e gli `stagers`: 
- `linsteners` ascoltano le connessioni con i targets
- `stagers` pezzi di codici o eseguibili che verranno eseguiti sul target e il modo in cui vengon trasgeriti sul target dipende dall'attaccante
- `agents` mostra i target e le loro informazioni

 Il primo passo é quello di creare un `listener`: 
 su Starkiller andare nella sezione che tratta i `listener` e nell'angolo in alto a destre cliccare su `crete` per poi selezionare il tipo di listener che vogliamo usare e in seguito le variabili che richiede la pagina e infine cliccare sul pulsante `summit` in alto a destra per far partire il listener. 

 > si consiglia di usare listener di tipo `http` o `http_hop`

Successivamente andiamo a chreare uno `stager`: 
su Starkiller andare nella sezione che tratta gli `stagers` e nell'angolo in alto a destra cliccare su `create` per poi inserire il tipo di stager che vogliamo usare e in seguito le variabili che richiede la pagina facendo attenzione ad inserire il listener corretto che abbiamo precedentemente creato.
infine clicchiamo sul pulsante `summit` in alto a destra per poi andare a visualizzare lo stager appena creato e scaricarlo in modo da poterlo trasferire sul target. 

 > si consiglia di usare uno stager di tipo `windows/csharp_exe` per i sistemi windows

Per poter trasgferire lo stager possiamo aprire un web server sulla cartella in cui é stato scaricato con il comando 
```bash
python3 -m http.server 80
#oppure
python -m SimpleHTTPServer 80
```
per poi scaricarlo sulla macchina target. <br>
dopo che il target avrá eseguito il file eseguibile `.exe`, nella schermata che riguarda gli `agents` avremo il nostro target e cliccando sul nome, possiamo interagire con esso scrivendo dei comandi, visualizzando tutte le cartelle presenti, eccetera. 

possiamo interagire con l'`agent` anche tramite il client empire precedentemente avviato scrivendo i comandi: 
```bash
agents
interact <nome>
help               # per visualizzare i comandi che possiamo eseguire
```
temiamo nota del fatto che Empire non é molto rapido e quindi ogni comando o azione che eseguiremo avrá un output in ritardo rispetto al nostro imput





