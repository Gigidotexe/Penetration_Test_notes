# Passive Information Gathering

La raccolta di informazioni passiva rappresenta una delle prime fasi di un penetration test, ed è fondamentale per acquisire dati utili senza interagire direttamente con il target. Questo approccio è invisibile dal punto di vista dell’infrastruttura bersaglio, perché non genera traffico diretto verso il sistema analizzato. Lo scopo è raccogliere quante più informazioni possibile sfruttando esclusivamente fonti OSINT (Open Source Intelligence), come motori di ricerca, dati DNS pubblici, archivi storici del web e strumenti automatici.

Un primo passo tipico è la risoluzione DNS, per ottenere l'indirizzo IP associato a un dominio. Utilizzando il comando `host <url>`, è possibile risalire rapidamente all'IP di un sito. Questa informazione, pur semplice, può essere molto utile per successive analisi, anche se ogni approfondimento tecnico sui DNS sarà trattato in un file separato dedicato esclusivamente alla DNS enumeration.

Un altro file importante è `robots.txt`, presente nella directory principale di molti siti web. Questo file nasce per impedire ai crawler dei motori di ricerca di indicizzare certe sezioni del sito. Tuttavia, può contenere riferimenti a directory sensibili che, pur essendo "nascoste" ai motori, sono comunque accessibili manualmente.

Per identificare la tecnologia web utilizzata da un sito, si possono usare strumenti come BuiltWith (disponibile anche come estensione browser) oppure da terminale `whatweb <url>`. Questo comando è molto potente perché analizza anche header HTTP, redirect, firme digitali dei server e altre caratteristiche tecniche per dedurre CMS, framework, librerie e plugin utilizzati dal sito.

Per salvare e studiare localmente l'intera struttura di un sito web si può usare HTTrack, un tool utile anche su Kali Linux. Basta installarlo con `sudo apt-get install webhttrack`. HTTrack consente di scaricare in locale tutte le pagine HTML, le risorse statiche, le directory, permettendo uno studio accurato offline.

Uno strumento semplice ma prezioso è `whois <url>`, che fornisce informazioni sulla registrazione di un dominio: registrar, name server, date di creazione e scadenza, e, se non oscurate dal GDPR, anche dati del registrante. Molti provider nascondono questi dati per privacy, ma quando disponibili offrono ottimi spunti per social engineering.

Per ottenere un quadro tecnologico più ampio su un sito si può utilizzare Netcraft, che fornisce dettagli sul sistema operativo, provider di hosting e altre informazioni d’infrastruttura. È disponibile in versione web, ma può anche essere automatizzato con script.

Un sito molto utile è Dnsdumpster, che fornisce una visualizzazione grafica dei sottodomini rilevati tramite tecniche OSINT. Anche in questo caso, rimando alla sezione DNS per dettagli più tecnici.

Per verificare se un sito è protetto da un Web Application Firewall (WAF), si può usare `wafw00f <url>`. Questo tool invia richieste HTTP e analizza le risposte per identificare la presenza e il tipo di WAF (es. Cloudflare, AWS WAF, Imperva, ecc.). È scaricabile da GitHub: 
`https://github.com/EnableSecurity/wafw00f`

Con il comando `wafw00f -l` è possibile elencare tutti i WAF rilevabili dal tool.

Per scoprire sottodomini legati a un dominio principale, uno strumento efficace è Sublist3r, scritto in Python. Può essere scaricato da GitHub:
`https://github.com/aboul3la/Sublist3r`

Esempio d'uso:
`sublist3r -d <url> -e <motori_di_ricerca> -o <outputfile>`

Può utilizzare vari motori di ricerca OSINT, ma per via delle limitazioni imposte da Google, è consigliabile eseguire Sublist3r dietro VPN per evitare blocchi temporanei.

Un’altra tecnica passiva molto potente è l’uso degli operatori avanzati di Google. Ad esempio:
`site:<dominio> inurl:<parola>`

Questo limita la ricerca a URL di uno specifico sito contenenti la parola indicata.

L’operatore `intitle:"index of"` è celebre per scoprire directory browsing attivi: questi sono spesso indice di file system web esposti senza controlli di accesso. Può rivelare repository di file, backup o script dimenticati.

La Wayback Machine, disponibile all'indirizzo `https://wayback-api.archive.org/save`, permette di vedere versioni storiche di un sito. È utile per recuperare informazioni rimosse, monitorare modifiche nel tempo o individuare configurazioni vulnerabili del passato.

TheHarvester è un altro tool molto potente per la raccolta OSINT. Permette di ottenere email, hostnames e sottodomini da fonti pubbliche. È disponibile su GitHub:
`https://github.com/laramies/theHarvester`

Sulle distro Debian-based può essere installato con:
`sudo apt-get install theharvester`
