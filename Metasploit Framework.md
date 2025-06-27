# Metasploit Framework

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

## Architettura del Framework

Il Metasploit Framework è suddiviso in diverse **categorie di moduli**, ciascuna con uno scopo preciso:

- **Exploit**: codice che sfrutta una vulnerabilità conosciuta. <br>
- **Payload**: codice che viene eseguito sul sistema target dopo che l’exploit ha avuto successo. <br>
- **Encoder**: moduli che permettono di **offuscare il payload**, aiutando ad evitare il rilevamento da parte degli antivirus. <br>
- **NOP Generator**: garantisce che il payload abbia una dimensione coerente e stabile inserendo "No Operation Instructions". <br>
- **Ausiliary Modules**: moduli non legati a payload o exploit, utilizzati per **scansioni**, **enumerazioni**, **attacchi DoS**, **sniffing**, **brute-force**, **port scanning**, o per operazioni svolte in contesti dove si è già all'interno della rete bersaglio. Questi moduli, infatti, sono utilissimi quando si ha accesso a una macchina compromessa in una rete interna, permettendo di eseguire analisi locali senza dipendere da strumenti esterni come Nmap. <br>

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

### Tipologie di Payload

- **Non-Staged Payload**: viene inviato completamente in un’unica fase insieme all’exploit. È statico, diretto e meno flessibile. <br>
- **Staged Payload**: viene inviato in **due fasi**:
  - **Stager**: stabilisce la connessione iniziale tra target e attaccante. <br>
  - **Stage**: vero e proprio payload che viene scaricato ed eseguito successivamente. <br>
Questo tipo è molto usato perché riduce le dimensioni iniziali dell’exploit e consente più flessibilità nella consegna. <br>

---
