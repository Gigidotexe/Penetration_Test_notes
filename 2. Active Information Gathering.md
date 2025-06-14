# Active Information Gathering

La raccolta attiva di informazioni rappresenta la fase in cui si inizia a interagire direttamente con il target. A differenza della fase passiva, in cui ci si affida a fonti pubbliche e OSINT, qui si invia traffico verso il sistema obiettivo, assumendosi il rischio di essere rilevati da firewall, IDS o IPS. Questa fase è fondamentale per individuare host attivi, servizi in ascolto e configurazioni visibili dalla rete.

Un primo passaggio consiste nel verificare se l’host risponde al ping ICMP, usando un semplice comando come `ping <IP>`. Tuttavia, molti sistemi disabilitano le risposte ICMP, quindi è spesso necessario adottare alternative. In questi casi si può usare `nmap -sn` per uno scan senza port scanning, o opzioni più sofisticate (vedi sezione Nmap).

Il comando `netstat -antp` su macchine Linux permette di vedere le connessioni attive e le porte in ascolto. Questo è utile sia in fase di attacco (quando si ottiene accesso alla macchina) sia in fase di studio del comportamento di rete.

Wireshark, invece, consente di intercettare e analizzare il traffico. Usando il filtro `tcp` si possono isolare i pacchetti TCP. Altri filtri come `ip.addr == <IP>` oppure `http.request` aiutano a focalizzare l’analisi su target o protocolli specifici.

Strumenti come `arp-scan`, `ping sweep`, `traceroute`, e `hping3` sono utili per il mapping della rete. Ad esempio:

- `arp-scan -l`: per scansionare tutti i dispositivi connessi alla LAN.
- `traceroute <IP>`: per identificare i router intermedi tra noi e il target.
- `hping3 -S <IP> -p <porta>`: invia SYN per simulare una connessione TCP e valutare la risposta.

Un’altra tecnica è l’**enumerazione attiva DNS**, che include l’interrogazione diretta dei name server per ottenere informazioni su host interni o record specifici. Per approfondimenti su tecniche, record e strumenti si veda la sezione "DNS Enumeration".

Durante questa fase si può iniziare anche un approccio mirato alla scoperta di servizi aperti, porte vulnerabili e tecnologie esposte, come vedremo più nel dettaglio nella sezione "Nmap Scanning".
