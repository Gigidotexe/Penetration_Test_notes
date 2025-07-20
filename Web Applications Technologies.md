
# Web Application Technologies
---
Le tecnologie Client Side utilizzate dalle applicazioni web sono:
**HTML** (Hypertext Markup Language)
È il linguaggio di markup utilizzato per strutturare e definire il contenuto delle pagine web. Fornisce le fondamenta per creare il layout e la struttura dell’interfaccia utente. <br> 
**CSS** (Cascading Style Sheets) <br> 
Definisce la presentazione e lo styling delle pagine web. Permette di controllare colori, font, layout e altri aspetti visivi dell’interfaccia. <br> 

**JavaScript** <br> 
Linguaggio di scripting che abilita l’interattività nelle applicazioni web. Viene usato per creare elementi UI dinamici e reattivi, gestire le azioni dell’utente ed eseguire validazioni lato client. <br> 

**Cookie e Local Storage** <br> 
Meccanismi client‑side per conservare piccole quantità di dati nel browser dell’utente. Sono spesso impiegati per la gestione delle sessioni e il “ricordami” delle preferenze. <br> 

<br>
Quelle Server Side sono: <br> 
**Web Server** <br>
Riceve e risponde alle richieste HTTP dai client (browser). Ospita i file dell’applicazione web, elabora le richieste e invia le risposte ai client. (Es. Apache 2, Nginx, Microsoft IIS). <br>

**Application Server** <br>
Esegue la logica di business dell’applicazione web. Processa le richieste degli utenti, accede ai database ed esegue calcoli per generare contenuti dinamici che il web server inoltra al client. <br>

**Database Server** <br>
Memorizza e gestisce i dati dell’applicazione web, come informazioni sugli utenti, contenuti e configurazioni. Risponde alle query per creare, leggere, aggiornare o cancellare record. (Es. MySQL, PostgreSQL, MSSQL, Oracle). <br>

**Linguaggi di scripting lato server** <br>
Tecnologie come PHP, Python, Java e Ruby vengono utilizzate per gestire l’elaborazione sul server. Interagiscono con i database, eseguono validazioni e generano contenuti dinamici da inviare al client. <br>

<br>

## Data Interchange
Lo scambio di dati (data interchange) permette a sistemi e applicazioni diversi di comunicare e condividere informazioni in modo coerente. <br>
Tramite la conversione dei formati (es. XML ↔ JSON), i dati diventano compatibili con il sistema ricevente, garantendo interoperabilità e corretto utilizzo indipendentemente da linguaggi, strutture o piattaforme diverse. <br> 

**Tecnologie utilizzate**
Le API (Application Programming Interfaces) sono interfacce che permettono a sistemi software differenti di comunicare e scambiarsi dati in modo standardizzato. Tramite chiamate REST, GraphQL o altri protocolli, le applicazioni web possono integrare servizi esterni, condividere informazioni e offrire funzionalità a terze parti, garantendo così flessibilità e interoperabilità tra piattaforme. <br> 

<br>

## Protocolli 
**JSON** (JavaScript Object Notation) <br>
Formato leggero e di uso diffuso per lo scambio di dati, facile da leggere e scrivere sia per gli umani sia per le macchine. Basato sulla sintassi di JavaScript, è utilizzato principalmente per trasmettere dati tra server e applicazioni web come alternativa a XML. <br> 

**XML** (eXtensible Markup Language) <br>
Formato versatile che utilizza tag definiti dall’utente per strutturare dati complessi e gerarchici. Spesso impiegato in file di configurazione, web services e scenari di scambio dati tra sistemi eterogenei. <br>

**REST** (Representational State Transfer) <br>
Stile architetturale che utilizza i metodi HTTP standard (GET, POST, PUT, DELETE) per gestire risorse e scambiare dati in modo semplice e leggero. È ampiamente adottato per la creazione di API web interoperabili su Internet. <br>

**SOAP** (Simple Object Access Protocol) <br>
Protocollo basato su XML che definisce un formato rigoroso (envelope, header, body) per lo scambio di informazioni strutturate. Offre funzionalità avanzate come transazioni, sicurezza e affidabilità dei messaggi, ed è spesso utilizzato in contesti enterprise. <br>

<br>

## Tecnologie di Sicurezza
Le tecnologie di sicurezza per le applicazioni web si focalizzano innanzitutto su due macro aree: autenticazione & autorizzazione e crittografia delle comunicazioni. <br>

**Autenticazione e autorizzazione** <br>
L’autenticazione consente di verificare in modo sicuro l’identità degli utenti (per esempio tramite password, token o sistemi basati su certificati), mentre l’autorizzazione stabilisce quali parti dell’applicazione ciascun utente può effettivamente usare o quali azioni può compiere in base al proprio ruolo e ai permessi assegnati. <br>
Insieme, questi meccanismi garantiscono che solo utenti legittimi possano accedere ed eseguire operazioni coerenti con la loro funzione aziendale. <br>

**Crittografia (SSL/TLS)** <br>
Per proteggere i dati in transito tra client e server, si utilizza SSL (Secure Socket Layer) o, più comunemente oggi, il suo successore TLS (Transport Layer Security). <br>
Questi protocolli cifrano l’intera comunicazione HTTP, impedendo a eventuali intercettatori di leggere o manomettere i contenuti delle richieste e delle risposte. <br>
In questo modo, credenziali, token di sessione e informazioni sensibili viaggiano sempre in forma criptata, assicurando la riservatezza e l’integrità dei dati trasmessi. <br>

<br>

## Tecnologie Esterne
Le tecnologie esterne arricchiscono e potenziano le applicazioni web sotto due aspetti principali: <br>

**Content Delivery Network** (CDN) <br>
Una CDN distribuisce file statici—come immagini, fogli di stile CSS e librerie JavaScript—su una rete di server geograficamente dislocati. <br>
In questo modo, le risorse vengono servite dall’edge server più vicino all’utente finale, riducendo la latenza, bilanciando il carico e garantendo maggiore disponibilità anche in caso di picchi di traffico o guasti localizzati. <br>

**Librerie e Framework di Terze Parti** <br>
Per accelerare lo sviluppo e integrare funzionalità avanzate senza reinventare la ruota, le web application utilizzano componenti esterni (framework UI, toolkit di autenticazione, moduli di analisi, ecc.). <br>
Sebbene offrano notevoli vantaggi in termini di produttività e feature set, è essenziale mantenere aggiornate queste dipendenze per evitare di importare vulnerabilità già note. <br>

---

