# DNS Enumeration

Durante un penetration test, l’enumerazione DNS è una delle attività chiave per raccogliere informazioni sull'infrastruttura di rete. Consente di identificare nomi host, sottodomini, server di posta, configurazioni errate e persino sistemi interni. Questa tecnica rientra nella raccolta attiva perché comporta interrogazioni dirette ai name server.

## Tipi di record DNS utili

Tra i record DNS più rilevanti troviamo:

- `A`: associa un dominio a un indirizzo IPv4.
- `AAAA`: per indirizzi IPv6.
- `MX`: specifica i mail server per il dominio.
- `NS`: elenca i name server autorevoli.
- `CNAME`: definisce alias per altri nomi.
- `PTR`: usato per la risoluzione inversa da IP a hostname.
- `TXT`: contiene metadati, spesso relativi a sicurezza (es. SPF, DKIM).
- `SOA`: record d’inizio autorità per la zona DNS.
- `SRV`: indica la posizione di servizi specifici (es. Active Directory).
- `HINFO`: contiene dettagli su sistema operativo e architettura.

## Strumenti utili

### Dig

`dig` è uno strumento versatile per interrogare i name server:

- `dig A <dominio>`
- `dig MX <dominio>`
- `dig NS <dominio>`
- `dig ANY <dominio>` (non sempre supportato)
- `dig -x <IP>` per risoluzione inversa

### Dnsrecon

`dnsrecon -d <dominio>` raccoglie tutti i record principali (A, NS, SOA, MX, TXT) e tenta brute force sui sottodomini se richiesto. È incluso in Kali Linux.

### Dnsenum

`dnsenum <dominio>` è utile per ricostruire la mappa DNS e testare la possibilità di zone transfer. Supporta l’uso di wordlist.

Esempio:

`dnsenum --dnsserver <server> --threads 10 -f /usr/share/wordlists/dnsmap.txt <dominio>`

### Fierce

`fierce -dns <dominio>` identifica host, sottodomini, reti interne e cerca eventuali errori di configurazione. Semplice da usare ma molto potente.

### Zone Transfer (AXFR)

Una zona DNS mal configurata può permettere il trasferimento completo dei record, rivelando l’intera mappa interna del dominio.

Esempio:

`dig AXFR @<name_server> <dominio>`

Questa tecnica funziona solo se i server sono vulnerabili (raro ma ancora possibile in ambienti legacy o test).

## Enumerazione passiva di sottodomini

Anche se non comporta traffico diretto al target, può essere utile integrare l’enumerazione attiva con fonti OSINT come:

- `crt.sh`: motore di ricerca di certificati SSL pubblici.
- `SecurityTrails`, `VirusTotal`, `Spyse`, `Amass`, `Subfinder`.

Molti strumenti come `dnsrecon` o `Amass` permettono di combinare approcci attivi e passivi nella stessa sessione.

## Risoluzione inversa

Ottenere il nome associato a un IP aiuta a comprendere la funzione di un host. Si usa:

`dig -x <IP>`

Questo tipo di informazione è molto utile quando si ha accesso a blocchi IP interni o quando si scoprono servizi con reverse DNS configurato.

La DNS Enumeration è uno dei tasselli più efficaci per comprendere la struttura logica e fisica di una rete, soprattutto quando si lavora senza credenziali o accesso preventivo.
"""
