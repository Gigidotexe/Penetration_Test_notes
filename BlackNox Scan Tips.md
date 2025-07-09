
# BlackBox 

[definizione di black box]

---

### Nmap
Prima dobbiamo scoprire quanti e quali host sono raggiungibili dalla nostra macchina attaccante e quindi effettuiamo una scansione nmap sulla rete senza effettuare un port scanning:
```bash
nmap -sn <networdk> 
```
<br>

Successivamente facciamo una **SYN Scan** (`-sS`) che é veloce e discreta, perché non completa la 3-way handshake (RST arriva subito dopo SYN-ACK) evitando di generare log di connessioni complete sul target, anche se i firewall IDS evoluti la possono comunque rilevare. 
```bash
nmap -sS -p- <IP>
```
<br>

Rimaniamo nella **SYN Scan** (`-sS`) ma effettuiamo un **OS Discovery** (`-O`) e un **Service Detection** (`-sV`) dei servizi in esecuzione sul target:
```bash
nmap -sS -p- -sV -O <IP>
```
<br>

Dopo aver identificato servizi aperti, lanciamo degli script NSE mirati sui servizi: 
```bash
ls /usr/share/nmap/scripts | grep <servizio>  # identifichiamo quali script sono disponibili
nmap -sV --script=<nomeScript.nse> <IP>
```
<br>

