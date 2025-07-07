
# Netcat Foundamentals
**Netcat** è uno strumento da riga di comando versatile che permette di leggere e scrivere dati attraverso connessioni di rete utilizzando i protocolli TCP o UDP. <br>
Viene descritto come il “coltellino svizzero delle reti” perché consente di:
- stabilire connessioni TCP o UDP verso porte specifiche
- ascoltare su porte locali per accettare connessioni in entrata (diventando quindi un server improvvisato)
- trasferire file tra macchine
- eseguire test di porte aperte (port scanning basilare)
- effettuare il port forwarding o creare reverse shell per attività di penetration testing

é disponibile per tutti i sistemi operativi *nix (unix, linux, ecc) e Windows.

Netcat utilizza un architettura di comunicazione Cliet-Server con due modi:
- **Client Mode** che viene usata per la connessione verso qualsiasi porta TCP/UDP di una macchina in ascolto su una determinata porta
- **Server Mode** che viene usata per mettere la nostra macchina attaccante in ascolto tramite una porta in modo da ricervere infomrazioni o connessioni
> netcat server non é la stessa cosa del server python perché netcat prende solo pacchetti raw mentre il server python accetta metodi http.

Alcune opzioni che possiamo includere nel comando netcat sono: 
```bash
-v            # aumenta la verbositá facendo scrivere in output piú informazioni sul collegamento
-u            # serve per specificare che vogliamo usare il protocollo UDP al posto di TCP (default)
-l            # serve per impotare netcat in modalitá listener (Server Mode)
-p <PORT>     # specifica la porta su cui vogliamo effettuare la connessione o la porta che vogliamo sia in ascolto sulla nostra macchina locale
-w <secondi>  # imposta un timeout per la connessione e quando quel tempo viene esaurito, netcat viene chiuso
-e            # esegue un programma subito dopo la connessione (es. nc -l -p 4444 -e /bin/bash)
-z            # serve per effettuare una connessione netcat senza inviare informazioni e viene spesso usato per vedere se una porta é aperta
-n            # disabilita la risoluzione DNS per fare le cose più velocemente o evitare traffico DNS inutile.
```
se vogliamo effettuare una connessione verso una macchina con una porta aperta, basta omettere l'opzione `-l`. <br>

---

### Port Scanning con NetCat
possiamo anche vedere se una porta é aperta usando netcat con il comando: 
```bash
nc -nv <IP> <PORT>
```
> -n disabilita la risoluzione dns
> -v aumenta la verbosità

se tramite questo comando dovessimo avere un output con scritto `Ncat: Connected to <IP>` significa che la porta é aperta anche se non abbiamo ricevuto nessun banner. <br> 

---

### NetCat Listener
per poter mettere in ascolto netcat, usiamo il comando: 
```bash
nc -nvlp <PORT>
```
se volessimo mettere in ascolto netcat in modo che un client windows possa connettersi, é necessario che il binario di netcat sia sulla macchina target windows. <br>
generalmente il binario si trova già su kali linux al path `/usr/share/windows-binaries/nc.exe` che possiamo trasferire su un target windows usando un fileserver in locale sulla nostra macchina attaccante contenente il binario `nc.exe` per poi eseguire il comando netcat con l'opzione `-e` in modo da installare ed eseguire il binario di netcat per windows. 
> questo é possibile solo se la macchina windows target ha una porta in ascolto.

possiamo quindi aprire un server locale sulla nostra macchina attaccante tramite il comando python:
```bash
python -m SimpleHTTPServer 80
#oppure
python3 -m http.server 80
```

---

### Trasgerire file tramite Netcat
Possiamo anche trasferire dei file tra due dispositivi connessi tramite netcat usando il comando:
```bash
#Windows Listener
nc -nvlp PORT > FILE.EXE
```
```bash
#Kali Linux Client
nc -nv IP PORT < FILEDATRASFERIRE.EXE
```

---
