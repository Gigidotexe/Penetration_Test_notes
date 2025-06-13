La raccolta attiva di informazioni rappresenta la fase successiva, nella quale si interagisce direttamente con il target. 
I DNS (Domain Name System) sono fondamentali per il funzionamento di Internet: traducono nomi di dominio leggibili dall'uomo (come www.sito.com) in indirizzi IP numerici utilizzabili dai dispositivi. 
I record DNS sono i componenti chiave di questo sistema:<br>
-A: associa un dominio a un indirizzo IPv4<br>
-AAAA: associa un dominio a un indirizzo IPv6<br>
-NS: (Name Server) indica i server autorevoli che gestiscono la zona DNS per un determinato dominio<br>
-MX: (Mail Exchange) identifica il server di posta responsabile per il dominio, il che può essere utile per attacchi mirati via e-mail<br>
-CNAME: (Canonical Name) crea un alias tra due nomi di dominio <br>
-TXT: viene usato spesso per verifiche <br>
-HINFO: contiene informazioni sul sistema operativo e l'hardware del server.<br>
-SOA: (Start of Authority) specifica l’inizio dell’autorità di una zona DNS e contiene informazioni come il server primario, l’e-mail dell’amministratore, il numero di serie della zona e i timer per il refresh o la scadenza della cache <br>
-SRV: (Service) è usato per definire la posizione di servizi come VoIP o directory (es. Active Directory) specificando porta, protocollo e priorità<br>
-PTR: usato nella risoluzione inversa, trasformando un indirizzo IP in un hostname<br>

Il file  `/etc/hosts` consente di associare manualmente un hostname a un IP locale, bypassando il DNS. È utile per accedere a interfacce di configurazione (come i router) senza dover ricordare l’IP o per testare modifiche locali prima della pubblicazione pubblica del DNS. È necessario avere i privilegi di superutente per modificarlo.

Per l’enumerazione DNS attiva, strumenti come `fierce -dns <url>` e  `dnsenum <url>` sono molto potenti. 
Fierce tenta di trovare sottodomini e possibili errori di configurazione, mentre dnsenum esegue una ricerca esaustiva dei record DNS e cerca di ricostruire la struttura DNS del dominio.

Per il monitoraggio delle connessioni di rete attive, `netstat -antp`
mostra le connessioni TCP aperte, lo stato delle porte e i processi associati. È un ottimo comando per analizzare eventuali attività sospette o debugging durante una sessione di attacco o difesa.

In Wireshark, inserire `tcp` nel campo di filtro permette di visualizzare solo i pacchetti che utilizzano il protocollo TCP, facilitando l’analisi del traffico in tempo reale o dei file pcap salvati.

Nmap è uno dei tool più potenti per la scansione di rete. Serve a identificare host attivi, servizi esposti, porte aperte, sistema operativo e possibili vulnerabilità. È uno strumento fondamentale per ogni penetration tester e amministratore di rete. 

Tuttavia, su Windows, spesso i ping ICMP sono bloccati per motivi di sicurezza; quindi, una normale scansione potrebbe non rilevare host attivi. Per ovviare a questo problema, Nmap consente l’uso di metodi alternativi di ping.

Il flag `-PS` invia pacchetti TCP SYN verso porte specificate (default sulla 80) per identificare host attivi. Un esempio è nmap `-sn -PS1-100 <IP>` dove `-sn` indica che non verrà effettuata una scansione delle porte ma solo un ping per vedere se l’host è online. Il pacchetto SYN è tipico dell'inizio di una connessione TCP: se si riceve un pacchetto SYN-ACK in risposta, l’host è considerato attivo.

Un altro metodo è l'ACK scan con nmap `-sn -PA <IP>` 
In questo caso viene inviato un pacchetto con il flag ACK. Se l’host non è attivo, non ci sarà risposta. Se invece l’host è attivo ma non ha una connessione TCP attiva su quella porta, risponderà con un pacchetto con flag RST. Tuttavia, alcuni firewall o IDS/IPS bloccano o reagiscono male a pacchetti con ACK settato senza un contesto di connessione, quindi questo metodo è meno affidabile.

Con `-PE` si utilizza il ping ICMP echo, il metodo classico del comando ping, che può essere filtrato da firewall o host configurati in modo restrittivo. Per evitare che la scansione venga bloccata a causa di host che non rispondono al ping, si può usare `nmap -Pn` che disabilita il ping e tratta tutti gli host specificati come attivi, forzando la scansione delle porte.

Per il rilevamento del sistema operativo, Nmap offre l’opzione `-O`, che cerca di identificare il sistema in base alle risposte dei pacchetti. Se la firma non è chiara, si può aggiungere `--osscan-guess` per forzare un tentativo di identificazione anche con dati incompleti o ambigui. Questo può aiutare in scenari dove le contromisure attive mascherano l’OS reale o nei casi in cui il sistema non risponde in modo coerente.

Il comando `nmap -sV --version-intensity <livellodiintensità>` serve per rilevare le versioni dei servizi in esecuzione sulle porte scoperte. Il parametro `--version-intensity` specifica quanto aggressivamente Nmap tenta di determinare la versione: 0 indica un rilevamento minimo e 9 (non 8, come erroneamente riportato) è il massimo livello di aggressività. Un livello più alto impiega più tempo ma aumenta l’accuratezza delle informazioni.

Gli script Nmap, chiamati anche NSE (Nmap Scripting Engine), sono dei moduli Lua che estendono le funzionalità di Nmap durante una scansione. Sono salvati nel path /usr/share/nmap/scripts e coprono molte categorie: da script di rilevamento di vulnerabilità a enumerazione di servizi, brute force, evasione di IDS e raccolta di metadati. È possibile trovarli facilmente usando 
`ls /usr/share/nmap/scripts | grep <servizio>`. 
Per eseguire uno script specifico si utilizza `nmap --script=<nome_script>`, mentre per eseguire tutti gli script di una determinata categoria, si può scrivere ad esempio `--script=http-*`.

Per trovare informazioni più approfondite su uno script, il comando 
`nmap --script-help=<nome_script>`
fornisce una descrizione dettagliata, incluso cosa fa e quali parametri si possono settare. Quando si usa il flag `-sC`, vengono applicati automaticamente gli script di default, che includono una raccolta selezionata utile per identificare servizi e vulnerabilità comuni in fase di ricognizione.





