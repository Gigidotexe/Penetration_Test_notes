
# Gophish
GoPhish è una piattaforma open‑source progettata per semplificare la creazione, l’esecuzione e l’analisi di campagne di phishing simulate a scopo di security awareness e penetration testing. Sviluppato in Go, si installa rapidamente su Linux, macOS o Windows e fornisce sia un’interfaccia web intuitiva sia un’API REST per automazioni personalizzate.  <br>

### Architettura
**Server Web**: ospita la dashboard da cui configuri target, email, landing page e tracciamento. <br>
**Database**: leggero (SQLite di default, ma supporta MySQL/PostgreSQL) per memorizzare utenti, template e risultati. <br>
**SMTP Relay**: puoi collegare il tuo server di posta o usare provider esterni per inviare le email di prova, mantenendo alta deliverability. <br>

### Workflow di una campagna
**Preparazione**: importa o crea la lista dei destinatari; raccogli informazioni (“ricognizione”) per personalizzare le email. <br>
**Creazione del template**: usa l’editor drag‑and‑drop o carica HTML personalizzato; includi variabili dinamiche (es. {{.FirstName}}). <br>
**Landing page**: configura pagine web clonate (login, form) per raccogliere credenziali o tracciare click. <br>
**Invio**: programma l’invio massivo o scaglionato; GoPhish gestisce bounce e rimbalzi. <br>
**Monitoraggio**: in tempo reale, vedi aperture, click, invii di credenziali e geolocalizzazione degli accessi. <br>

Gophis é disponibile al suo <a href="https://getgophish.com/">Sito Ufficiale</a> e nella sua <a href="https://github.com/gophish/gophish">Repository GitHub</a>.

### Utilizzo
Dopo aver clonato la repository, ci troveremo davanti una cartella contenente dei template html da poter usare e un file chiamato `confing.json` da dover setuppare: 
aprendo il file ci troveremo tre sezioni `admin_server`, `phish_server` e la sezione per il database dove verranno memorizzate le informazioni ottenute durante la campagna di phishing. <br>

Per poter iniziare, basterà avviare il file `gophish.exe` e successivamente andare nel `admin_server` tramite il browser usando l'indirizzo specificato e la porta e successivamente dovremo effettuare l'accesso al nostro account gophish. <br>
Adesso, dobbiamo impostare il sending profile ovvero il profilo che invierà le email di phishing. <br>
Successivamente, possiamo creare la landing page che sarà la pagina in cui si troverà la vittima una volta che avrà cliccato sul link malevolo. <br> 

dopo aver impostato la campagna e inviate le mail, avremo un report su tutte le azioni che sono avvenute tramite vari grafici.
