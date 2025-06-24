## Scansione e Identificazione del Servizio
Il primo passo é quello di verificare che servizio é in esecuzione sulla macchina target.
### Nmap
Per verificare se SSH è attivo e quale versione è in esecuzione:
```bash
nmap -sV -p 22 <IP>
```

Un banner può rivelare, ad esempio:
```
22/tcp open ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.10
```
### Metasploit 
Possiamo usare un modulo di Metasploit per verificare la versione del servizio
```bash
use auxiliary/scanner/ssh/ssh_version
```

Dopo aver infividuato che la porta sia aperta e la versione del servizio in esecuzione su essa, possiamo procedere con la fase di Exploiting
