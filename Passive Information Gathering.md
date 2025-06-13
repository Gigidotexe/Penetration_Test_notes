La raccolta di informazioni passiva è una tecnica fondamentale nell’ambito della cybersecurity, in particolare durante la fase di ricognizione (reconnaissance) di un penetration test. Consiste nell’ottenere dati rilevanti su un bersaglio senza interagire direttamente con esso, quindi senza generare traffico visibile o allarmare eventuali sistemi di difesa. Questo processo si basa sull’analisi di informazioni pubblicamente accessibili, spesso dimenticate o trascurate, ma potenzialmente molto sensibili.

Per cominciare, uno dei metodi più basilari per ottenere informazioni su un sito è la risoluzione DNS e la scoperta dell’indirizzo IP associato al dominio. Utilizzando comandi come `host <url>`, si può ottenere l’IP direttamente. A livello informativo, la struttura dei siti può anche essere dedotta dal file robots.txt, un file di testo posto nella directory principale di molti siti web. Questo file indica ai crawler dei motori di ricerca quali sezioni del sito non devono essere indicizzate, ma spesso contiene riferimenti a directory sensibili che, pur non essendo destinate al pubblico, sono comunque accessibili a chi conosce il loro nome.

Per identificare la tecnologia utilizzata da un sito web, si possono usare strumenti come BuiltWith, disponibile come estensione per browser, che rileva CMS, framework, librerie e plugin. La sua controparte da terminale, `whatweb <url>`, permette di ottenere informazioni simili tramite la CLI, con il vantaggio di rilevare anche comportamenti legati a redirect, header HTTP e possibili impronte digitali del server.

Un'altra tecnica di raccolta passiva consiste nel copiare interamente il sito web in locale per analizzarne struttura e contenuti. HTTrack è uno strumento efficace per questo scopo, disponibile anche su Linux. Il suo uso permette di studiare il codice HTML, le directory e le risorse statiche di un sito, senza accedere direttamente a funzionalità riservate.
Per installarlo usare il comando `sudo apt-get install webhttrack`

Il comando `whois <url>` permette di raccogliere informazioni sulla registrazione di un dominio, come il registrar, i name server, la data di creazione e scadenza, e talvolta dati personali o aziendali del registrante. Tuttavia, con l’entrata in vigore del GDPR molte di queste informazioni sono oscurate per proteggere la privacy degli utenti.

Servizi come Netcraft forniscono informazioni aggiuntive sulle tecnologie utilizzate da un sito, inclusi sistema operativo, provider di hosting e altri metadati. Anche se accessibili da browser, strumenti simili possono essere scriptati o automatizzati per analisi su larga scala.

Per quanto riguarda i DNS, esistono strumenti molto avanzati come `dnsrecon -d <url>`, già preinstallato in Kali Linux. Questo script in Python permette di enumerare i principali record DNS (come A, MX, TXT, SOA, NS) di un dominio, utile per scoprire server di posta, autorità di zona e altri dettagli infrastrutturali. Un’alternativa web-based è Dnsdumpster, un servizio che visualizza graficamente la mappa dei sottodomini e record DNS associati a un target.

Il record MX in particolare è rilevante per capire come un’organizzazione gestisce la posta elettronica. Spesso, identificando i server MX, si può risalire al provider di e-mail utilizzato, come Google Workspace o Microsoft 365, ottenendo così indizi utili per eventuali tentativi di phishing mirato.

Per capire se un sito è protetto da un WAF (Web Application Firewall), si può usare `wafw00f <url>`. Questo tool invia richieste HTTP normalizzate e analizza le risposte per determinare se un firewall applicativo è attivo, e in alcuni casi quale vendor lo ha prodotto (come Cloudflare, AWS WAF, Imperva, ecc.). Si può scaricare da GitHub e supporta il rilevamento di un gran numero di prodotti diversi.
Si può ottenere su GitHub(https://github.com/EnableSecurity/wafw00f) 
con `wafw00f -l` è possibile avere un elenco di tutte le web application firewall rilevabili da wafwof

Per identificare i sottodomini di un dominio principale, si può usare Sublist3r, uno script in Python che automatizza la ricerca dei sottodomini su diversi motori di ricerca. Google solitamente limita o blocca queste richieste, ma si può bypassare temporaneamente questa restrizione usando una VPN. Sublist3r può essere installato direttamente dai repository o scaricato da GitHub(https://github.com/aboul3la/Sublist3r)

Sublist3r è un tool Python estremamente utile per la raccolta passiva delle informazioni, in particolare per l'enumerazione dei sottodomini di un dominio principale.
Il comando:
`sublist3r -d <url> -e <motorediricerca, altromotorediricerca> -o <outputfile>`
consente di indicare il dominio da analizzare, specificare i motori di ricerca da utilizzare per l’enumerazione e definire un file di output per salvare i risultati.
Questo strumento utilizza fonti OSINT e può essere influenzato da limitazioni imposte dai motori di ricerca come Google, che spesso blocca le richieste automatizzate, motivo per cui è consigliabile usare una VPN.

Un'altra tecnica passiva molto efficace è l’uso di operatori di ricerca avanzata su Google. Per esempio
`site: <nomedelsito> inurl: <cosadevecontenere>`
consente di restringere i risultati della ricerca a pagine di uno specifico dominio che contengono determinati percorsi o parole chiave nell’URL
Tra i filtri più potenti c'è `“intitle: index of”`, che permette di cercare directory web esposte pubblicamente: spesso si trovano repository di file non protetti o pagine di directory browsing attivate accidentalmente.
Questo operatore cerca pagine il cui titolo contiene "Index of", tipico di quando un server Apache o Nginx non trova un file index.html o index.php nella cartella e mostra invece l’elenco dei file presenti.

Wayback Machine, disponibile al link (https://wayback-api.archive.org/save) consente di visualizzare vecchie versioni di un sito web archiviate nel tempo. Questo è utile per analizzare la cronologia di una pagina, trovare link o informazioni rimosse e comprendere l’evoluzione delle tecnologie utilizzate nel tempo. Molti siti potrebbero rivelare vulnerabilità precedenti che sono rimaste irrisolte.

TheHarvester è un tool progettato per raccogliere indirizzi email, nomi host e sottodomini attraverso motori di ricerca e database pubblici. È utile per la fase di ricognizione e può essere scaricato da GitHub all’indirizzo (https://github.com/laramies/theHarvester) 
Le vecchie versioni possono essere installate direttamente dai repository Debian-based con il comando: sudo apt-get install theharvester (con la H minuscola) 











