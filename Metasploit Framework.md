# Metasploit Framework

## Introduzione

Il **Metasploit Framework** è uno dei tool più utilizzati al mondo per il penetration testing. <br>
È stato inizialmente sviluppato da **H.D. Moore** nel 2003 in linguaggio **Perl**, per poi essere completamente riscritto in **Ruby** nel 2007. <br>
Nel **2009**, il progetto è stato acquistato dalla compagnia di sicurezza **Rapid7**, che ancora oggi ne mantiene lo sviluppo e l’hosting del codice sorgente su GitHub. <br>

Il framework è **open source** e modulare, pensato per automatizzare molte fasi del penetration test. <br>
Oltre alla sua enorme base dati di **exploit**, è anche utilizzato per **sviluppare e testare nuovi exploit**. <br>
Questo lo rende uno strumento fondamentale non solo per chi esegue test di sicurezza, ma anche per chi sviluppa software sicuro. <br>
Eseguire un test manualmente comporterebbe molto più tempo rispetto all’uso combinato di moduli automatizzati di Metasploit. <br>

Metasploit gestisce tutte le sue funzionalità tramite **moduli**: ogni modulo svolge una funzione ben definita (ad esempio un exploit o un’utility di scansione). Essendo **modulare**, consente agli sviluppatori e ai tester di aggiungere facilmente nuove funzionalità personalizzate. <br>

### Terminologia Fondamentale

- **Interfaccia**: modalità di interazione con Metasploit (CLI, GUI, API). <br>
- **Modulo**: porzione di codice che compie un’azione specifica (es. exploit, scanner, encoder, payload, etc.). <br>
- **Vulnerabilità**: una debolezza sfruttabile in un sistema o software. <br>
- **Exploit**: codice che sfrutta una vulnerabilità per ottenere accesso o eseguire comandi. <br>
- **Payload**: codice eseguito sul sistema bersaglio una volta che l’exploit ha avuto successo. <br>
- **Listener**: componente che resta in ascolto di una connessione in entrata dal target. <br>

### Interfacce di Metasploit

Metasploit è accessibile attraverso:

- **MSFconsole (CLI)**: è l’interfaccia principale del framework. Consente di interagire con i moduli, eseguire exploit, configurare payloads e monitorare le sessioni attive. È altamente personalizzabile tramite comandi e script. <br>

- **Community Edition (Web UI)**: Rapid7 fornisce anche una versione con interfaccia web gratuita chiamata **Metasploit Community Edition**, utile per chi preferisce una gestione grafica dei test. Questa versione permette di creare workspace, lanciare scan automatizzati e accedere a reporting strutturato. <br>

- **Armitage**: interfaccia grafica in Java pensata per ambienti di team collaboration, permette una visualizzazione interattiva degli host, moduli ed exploit. È molto utile in ambienti didattici o CTF. <br>

---

## Installazione

**Metasploit Framework** è distribuito da Rapid7 e può essere scaricato e installato come pacchetto standalone sia su **Windows** che su **Linux**. 
Generalmente, per i penetration tester, viene utilizzato all’interno di **Kali Linux**, dove è già preinstallato. 

Per installarlo manualmente su una distribuzione Linux:
```bash
sudo apt-get update && sudo apt-get install metasploit-framework
```

Una volta completata l’installazione, è necessario configurare il database PostgreSQL:
```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
sudo systemctl status postgresql
```

---

## MSF Database (MSFDB)

**MSFDB** è il database interno di Metasploit usato per memorizzare tutti i dati generati durante il penetration test, come risultati di scansione, vulnerabilità identificate, host scoperti, ecc. 
È basato su **PostgreSQL** e ne richiede l’esecuzione e la corretta configurazione per funzionare. 

Il database facilita anche l’importazione dei dati provenienti da altri strumenti di terze parti come **Nmap** o **Nessus**.

Per inizializzarlo:
```bash
sudo msfdb init
```
In caso di problemi:
```bash
sudo msfdb reinit
```
Per verificarne lo stato:
```bash
sudo msfdb status
```
Oppure direttamente dalla MSFconsole:
```bash
db_status
```

---

## Gestione dei Workspace

In Metasploit, un **workspace** è un ambiente isolato dove vengono memorizzati tutti i dati raccolti durante un test, come host, vulnerabilità, credenziali e risultati delle scansioni. 
Il workspace di default è chiamato `default`, ma è altamente consigliato creare un workspace separato per ogni test o cliente.

Tutti i dati dei workspace sono memorizzati dentro al **MSFDB**.

Comandi principali:
```bash
workspace                # mostra il workspace attuale e quelli disponibili
workspace -a <nome>     # crea un nuovo workspace e ci sposta dentro
workspace <nome>        # cambia workspace
workspace -d <nome>     # elimina un workspace
workspace -r <vecchio> <nuovo>  # rinomina un workspace
```
Per visualizzare la documentazione del comando:
```bash
workspace -h
```

Comando utile:
```bash
hosts    # mostra gli host salvati nel database del workspace corrente
analyze  # analizza degli exploit applicabili ad un host memorizzato 
```

---

## Architettura del Framework

Il Metasploit Framework è suddiviso in diverse **categorie di moduli**, ciascuna con uno scopo preciso:

- **Exploit**: codice che sfrutta una vulnerabilità conosciuta. <br>
- **Payload**: codice che viene eseguito sul sistema target dopo che l’exploit ha avuto successo. <br>
- **Encoder**: moduli che permettono di **offuscare il payload**, aiutando ad evitare il rilevamento da parte degli antivirus. <br>
- **NOP Generator**: garantisce che il payload abbia una dimensione coerente e stabile inserendo "No Operation Instructions". <br>
- **Ausiliary Modules**: moduli non legati a payload o exploit, utilizzati per **scansioni**, **enumerazioni**, **attacchi DoS**, **sniffing**, **brute-force**, **port scanning**, o per operazioni svolte in contesti dove si è già all'interno della rete bersaglio. Questi moduli, infatti, sono utilissimi quando si ha accesso a una macchina compromessa in una rete interna, permettendo di eseguire analisi locali senza dipendere da strumenti esterni come Nmap. <br>

### Tipologie di Payload

- **Non-Staged Payload**: viene inviato completamente in un’unica fase insieme all’exploit. È statico, diretto e meno flessibile. <br>
- **Staged Payload**: viene inviato in **due fasi**:
  - **Stager**: stabilisce la connessione iniziale tra target e attaccante. <br>
  - **Stage**: vero e proprio payload che viene scaricato ed eseguito successivamente. <br>
Questo tipo è molto usato perché riduce le dimensioni iniziali dell’exploit e consente più flessibilità nella consegna. <br>

---

## Meterpreter

**Meterpreter** (Meta-Interpreter) è un **payload avanzato** incluso nel Metasploit Framework, noto per la sua versatilità e furtività. <br>
Una volta eseguito sul sistema target:

- Risiede **solo in memoria** (RAM), quindi non lascia tracce sul disco rendendosi difficile da individuare. <br>
- Permette di **eseguire comandi sul target**, **esfiltrare file**, **attivare keylogger**, **catturare screenshot**, e molto altro. <br>
- Supporta il **pivoting** e l’**escalation di privilegi**. <br>
- Può caricare script di estensione per interagire con il sistema Windows in maniera più profonda (es. dumping di hash, token impersonation). <br>
- Permette di **migrare tra processi**, chiudere e riaprire sessioni in modo dinamico. <br>

---

## Filesystem di Metasploit

Il Metasploit Framework è installato solitamente nel percorso: `/usr/share/metasploit-framework/`

All’interno troviamo diverse directory, tra cui:

- `modules/` ⟶ contiene tutti i moduli del framework: exploit, payloads, auxiliary, encoder ecc. È possibile inserire anche moduli custom in queste sottocartelle. <br>
- `scripts/` ⟶ include script di esempio o script di automazione. <br>
- `data/` ⟶ contiene dizionari, file di configurazione, liste di utenti/password usate da moduli. <br>
- `lib/` ⟶ librerie Ruby utilizzate per il funzionamento interno del framework. <br>
- `plugins/` ⟶ plugin opzionali che possono estendere le capacità del framework (es. connessione a database o supporto a nuove tecnologie). <br>

---

## Fundamentals e Uso Pratico

Per poter utilizzare qualsiasi modulo in Metasploit, è essenziale **impostare correttamente le variabili richieste**, tra cui:

- `LHOST` ⟶ IP della macchina attaccante. <br>
- `LPORT` ⟶ porta su cui l’attaccante riceve la connessione dal target. <br>
- `RHOST` ⟶ IP del target. <br>
- `RHOSTS` ⟶ elenco di IP da attaccare (utile per attacchi multipli). <br>
- `RPORT` ⟶ porta del servizio da attaccare sul target. <br>

Per interagire con Metasploit:
- Pulire il terminale con `Ctrl + L`. <br>
- Scrivere `help` nella console per visualizzare tutti i comandi disponibili. <br>

Per cercare moduli o funzionalità:
```bash
search <parola_chiave>
```
Esempio:
```bash
search portscan
```

Per selezionare un modulo:
```bash
use <nome_modulo>
```

Per visualizzare le opzioni richieste da un modulo:
```bash
show options
```

Per impostare una variabile:
```bash
set RHOST 192.168.1.25
```

Per rimuovere una variabile:
```bash
unset RHOST
```

Per avviare un modulo dopo aver impostato tutto:
```bash
run
# oppure
exploit
```

Il comando `search` può essere raffinato con parametri:
```bash
search cve:2017 type:exploit
```
Per visualizzare le opzioni disponibili:
```bash
search -h
```

Per visualizzare i payload disponibili:
```bash
show payloads
```
E impostarne uno:
```bash
set payload <nome_payload>
```

Per monitorare le sessioni attive:
```bash
sessions
```
Per interagire con una specifica sessione:
```bash
sessions -i <ID_sessione>
```

### Comando `connect`

Il comando `connect` in Metasploit è simile a `telnet` o `nc`. Serve per connettersi manualmente a una porta di un host remoto e verificare se è aperta o interagire direttamente con il servizio:
```bash
connect <IP> <porta>
```
È utile ad esempio per testare banner o protocolli senza dover usare un modulo automatizzato.

---

## Port Scanning e Nmap in Metasploit

Metasploit supporta due modalità di integrazione con Nmap:

1. **Importazione delle scansioni**: dopo aver eseguito una scansione Nmap, possiamo esportare i risultati in formato XML:
```bash
nmap <IP> -oX output.xml
```
E importarli in Metasploit:
```bash
db_import <path_output.xml>
```

2. **Scansione diretta via db_nmap**: eseguendo direttamente la scansione dentro Metasploit, i dati verranno salvati automaticamente nel database:
```bash
db_nmap -sV -Pn 192.168.1.0/24
```

Una volta completata una scansione (via `db_nmap` o `db_import`), possiamo visualizzare:
- `hosts` ⟶ lista degli host scoperti.
- `services` ⟶ porte, protocolli e servizi rilevati.
- `vulns` ⟶ vulnerabilità associate agli host.

---

# Metasploit AutoPWN
Esiste un plugin che permette di effettuare delle scansioni automatiche delle vulnerabilitá su sistemi analizzati con Metasploit Framework. <br>
Il Plugin é scaricabile su <a href="https://github.com/hahwul/metasploit-autopwn">GitHub</a> <br>
Dopo aver scaricato il file, va spostato dentro la directory `/usr/share/metasploit-framework/plugins` con il comando `mv` e successivamente importarla dentro msfconsole con il comando: <br>
`load db_autopwn` <br> 
per poter utilizzare questo plugin, basta scrivere il comando `db_autopwn` seguito dell'opzione che vogliamo eseguire. <br>
per visualizzare il messaggio di aiuto, usiamo `db_autopwn -h` 

se usiamo `db_autopwn =p -t` verrá effettuata una ricerca di tutti gli exploit (`-t`)per tutte le porte che abbiamo trovato aperte (`-p`)dopo aver effettuato una scansione dentro msfconsole. <br>
se volessimo limitare la scansione solo a determinate porte aperte, potremmo usare il comando `db_autopwn =p -t -PI <PORT>`

---

## Automazione con Resource Scripts

Metasploit permette di automatizzare comandi ripetitivi tramite gli **script di risorse**, utilissimi per semplificare operazioni frequenti come la configurazione di un multi/handler. I file `.rc` possono essere scritti manualmente oppure generati automaticamente da sessioni precedenti.

Esempio di script `handler.rc`:
```bash
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <IP>
set LPORT <PORT>
run
```

Per eseguire uno script `.rc` all’avvio:
```bash
msfconsole -r /path/to/handler.rc
```

Oppure direttamente da MSFconsole:
```bash
resource /path/to/handler.rc
```

È possibile **generare uno script automaticamente** con tutti i comandi usati durante una sessione di lavoro, utilizzando:
```bash
makerc /path/to/output.rc
```

I file `.rc` possono essere salvati anche nella cartella predefinita:
`/usr/share/metasploit-framework/scripts/resource/`

---
