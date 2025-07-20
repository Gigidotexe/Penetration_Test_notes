
# Common Threats and Risks in Web Applications
Le minacce piú comuni di una web application sono: <br>

**Cross‑Site Scripting** (XSS)
L’attaccante inietta codice JavaScript (o altro script lato client) in pagine web visualizzate da altri utenti, ottenendo così la possibilità di rubare cookie di sessione, manipolare l’interfaccia utente o eseguire azioni a nome della vittima. <br>

**QL Injection** (SQLi)
Ssfruttando l’inserimento di comandi SQL malevoli in campi di input, l’attaccante può leggere, modificare o cancellare dati dal database, oppure ottenere l’accesso a tabelle non autorizzate. <br>

**Cross‑Site Request Forgery** (CSRF)
L’utente autenticato viene “indotto” tramite un link o un’immagine malevola a inviare richieste non autorizzate verso l’applicazione, sfruttando la sessione già valida. <br>

**Security Misconfigurations**
Impostazioni errate di server, framework o database possono rivelare informazioni sensibili (errori verbose, directory listing, endpoint di debug) o lasciare aperti servizi inutilizzati.
Esempio pratico: pannello di amministrazione non protetto da autenticazione, o server che espone `/phpinfo.php`. <br>

**Sensitive Data Exposure**
La mancata protezione adeguata di dati sensibili (password, token, informazioni personali) porta a violazioni di riservatezza e furto d’identità. <br>

**Brute‑Force** e **Credential Stuffing**
Attacchi automatici che tentano migliaia di combinazioni di username/password (brute‑force) o riutilizzano credenziali sottratte da altri data breach (credential stuffing). <br>

**File Upload Vulnerabilities**
Meccanismi di upload di file insicuri possono permettere l’inserimento di script o binari malevoli sul server, portando a Remote Code Execution o compromissione dell’infrastruttura. <br>

**Denial‑of‑Service** (DoS) e **Distributed Denial‑of‑Service** (DDoS)
Attacchi DoS e DDoS mirano a sovraccaricare i server dell’applicazione web con un volume eccessivo di richieste, causando interruzioni del servizio e negando l’accesso agli utenti legittimi. <br>

**Server‑Side Request Forgery** (SSRF)
L’attaccante induce il server dell’applicazione a effettuare richieste HTTP verso risorse interne o esterne non autorizzate, potenzialmente consentendo furto di dati o accesso a servizi privati. <br>

**Inadequate Access Controls**
Controlli di accesso deboli possono permettere a utenti non autorizzati di invocare funzionalità riservate o di visualizzare dati sensibili. <br>

**Utilizzo di componenti con vulnerabilità note**
L’integrazione di librerie, framework o moduli di terze parti affetti da falle di sicurezza già note introduce automaticamente quei punti deboli nell’applicazione web. <br>

**Broken Access Control**
Meccanismi di autorizzazione difettosi consentono a un attaccante di eludere restrizioni e compiere azioni o accedere a dati per i quali non possiede i permessi necessari. <br>



