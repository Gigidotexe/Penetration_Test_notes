# Active Information Gathering

La raccolta attiva di informazioni rappresenta una fase successiva rispetto alla raccolta passiva. In questo contesto, si interagisce direttamente con il target generando traffico verso di esso, il che può comportare il rischio di essere rilevati da sistemi IDS/IPS o firewall. Tuttavia, è un passaggio fondamentale per ottenere dati più dettagliati su host, servizi, configurazioni e potenziali vulnerabilità.

I DNS (Domain Name System) sono il primo punto di contatto. Essi traducono i nomi di dominio leggibili dall’uomo in indirizzi IP comprensibili per le macchine. I record DNS contengono informazioni preziose, tra cui:

- `A`: associa un dominio a un indirizzo IPv4.
- `AAAA`: per indirizzi IPv6.
- `NS`: indica i name server autorevoli.
- `MX`: identifica il server di posta (utile per attacchi via email).
- `CNAME`: crea alias tra nomi.
- `TXT`: spesso utilizzato per verifiche di proprietà o sicurezza (es. SPF, DKIM).
- `HINFO`: contiene informazioni su sistema operativo e hardware.
- `SOA`: identifica l’inizio dell’autorità di una zona DNS.
- `SRV`: specifica posizione di servizi come VoIP o Active Directory.
- `PTR`: per risoluzioni inverse IP → hostname.

Il file `/etc/hosts` consente l’associazione manuale tra hostname e IP locali. È utile per test locali o per bypassare temporaneamente il DNS. La sua modifica richiede privilegi di superutente.

Per l’enumerazione attiva dei DNS si possono usare:

- `fierce -dns <url>`: ricerca sottodomini e configurazioni DNS errate.
- `dnsenum <url>`: esegue una ricerca esaustiva dei record DNS e cerca di ricostruire la struttura DNS del dominio.

Per analizzare le connessioni di rete attive, il comando `netstat -antp` mostra le connessioni TCP aperte, lo stato delle porte e i processi collegati. È utile sia in fase di attacco che di difesa.

In Wireshark, applicando il filtro `tcp` si possono osservare solo i pacchetti TCP, rendendo più chiara l’analisi del traffico di rete in tempo reale o dei file `.pcap`.

### Nmap

Nmap è uno degli strumenti più potenti per la scansione attiva. Consente di identificare host attivi, porte aperte, versioni dei servizi e sistemi operativi. Tuttavia, su Windows, spesso i ping ICMP sono bloccati. Per ovviare a questo, Nmap permette tecniche alternative:

- `nmap -sn -PS1-100 <IP>`: invia pacchetti TCP SYN su porte specifiche per identificare host attivi.
- `nmap -sn -PA <IP>`: usa pacchetti TCP ACK (meno affidabile a causa dei firewall).
- `nmap -PE <IP>`: utilizza ping ICMP echo (potrebbe essere bloccato).
- `nmap -Pn <IP>`: disabilita il ping e forza la scansione delle porte come se l’host fosse attivo.

Per l’identificazione del sistema operativo:

- `nmap -O`: cerca di identificare l’OS in base ai pacchetti di risposta.
- `--osscan-guess`: forza un tentativo anche con risposte incerte.

Per determinare la versione dei servizi:

- `nmap -sV --version-intensity <livello>`: 0 è minimo, 9 è massimo livello di rilevamento.

Gli script NSE (Nmap Scripting Engine), scritti in Lua, estendono Nmap. Si trovano nel path `/usr/share/nmap/scripts` e possono essere invocati con:

- `nmap --script=<nome_script>`
- `nmap --script=http-*` (per tutti gli script relativi a HTTP)
- `nmap --script-help=<nome_script>`: per visualizzare la documentazione dello script.

Il flag `-sC` applica gli script di default, molto utili per identificare servizi e vulnerabilità comuni.
