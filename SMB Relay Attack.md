<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
# SMB Relay Attack – Tecnica, Configurazione e Spiegazioni

L’**SMB Relay Attack** è una tecnica di attacco di tipo man-in-the-middle (MITM) in cui un attaccante intercetta una richiesta SMB legittima e la inoltra verso un altro sistema, impersonando l’utente vittima e ottenendo così accesso non autorizzato a risorse sensibili.

È particolarmente efficace nei sistemi Windows, dove SMB è comunemente usato per la condivisione di file, stampanti e risorse di rete.

---

## Funzionamento dell’attacco SMB Relay

### Intercettazione del traffico SMB
Un attaccante si posiziona tra un client e un server SMB legittimo utilizzando tecniche MITM come:
- **ARP Spoofing**
- **DNS Spoofing**
- **Server SMB malevolo fittizio**

### Acquisizione degli hash NTLM
Quando un client si connette a un server SMB, invia un pacchetto di autenticazione NTLM. <br>
L’attaccante, in posizione MITM, intercetta questi hash senza doverli decifrare.

### Relay su server legittimo
L’hash NTLM intercettato viene semplicemente **inoltrato** a un altro server SMB che si fida della richiesta. <br>
Questo consente all’attaccante di **impersonare l’utente** la cui autenticazione è stata intercettata.

### Accesso non autorizzato
Se il relay ha successo, l’attaccante ottiene l’accesso alle risorse disponibili su quel server con i privilegi dell’utente vittima (che potrebbero includere file, database, o privilegi amministrativi).

---

## Esecuzione dell’attacco con Metasploit

Metasploit fornisce un modulo dedicato:

`use exploit/windows/smb/smb_relay`

Configurazioni necessarie:
- `SRVHOST` ⟶ l’IP della macchina attaccante.
- `SMBHOST` ⟶ l’IP del server SMB target a cui inoltrare l’autenticazione.
- `LHOST` ⟶ IP dell’ascoltatore della macchina attaccante (per reverse shell).

Dopo aver eseguito l'exploit, il modulo si mette in attesa e nel momento in cui la macchina attaccante riceverà una richiesta di connessione a un server SMB, il modulo smb_relay prenderà la richiesta di autenticazione NTLM e la inoltrerà verso il server SMB cosí da ottenere una sessione come se fossimo la macchina vittima. 

---

## DNS Spoofing – Preparazione

Il comando:
```bash
echo "<IPattaccante> *.sportsfoo.com" > dns
```
crea un file chiamato `dns` che associa **qualsiasi sottodominio di sportsfoo.com** all’indirizzo IP dell’attaccante. <br>
Questo file viene poi utilizzato da `dnsspoof` per falsificare risposte DNS, dirottando richieste DNS legittime verso l’attaccante.

Questo passaggio è fondamentale perché consente alla vittima di tentare la connessione SMB verso l’host controllato dall’attaccante.

Poi eseguiamo:
```bash
dnsspoof -i eth1 -f dns
```
- `-i eth1` ⟶ specifica l’interfaccia da usare per il listening (es. eth1).
- `-f dns` ⟶ indica il file contenente i record DNS falsificati.

Questo avvia il processo di DNS Spoofing sulla rete che, nel momento in cui verrá ricevuta una richiesta per risolvere l'indirizzo di un dominio o sottodominio del server SMB(sportsfoo.com) verrá indicato che quell'dominio ha come indirizzo IP quello dell'attaccante, cosí da ricevere la richiesta di autenticazione da parte della vittima da poter inoltrare al server SMB tramite il modulo `smb_replay` in attesa.

---

## ARP Spoofing e IP Forwarding – Man in the Middle

### Abilitazione dell’IP Forwarding
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```
Questo comando **abilita l’inoltro dei pacchetti IP** sulla macchina Kali. <br>
È necessario per permettere alla macchina attaccante di comportarsi come router tra la vittima e il gateway.

### Avvio dell’ARP Spoofing

Nel primo terminale:
```bash
arpspoof -i eth1 -t <targetIP> <gateway>
```
- `-i eth1` ⟶ interfaccia da usare.
- `-t <targetIP>` ⟶ IP della vittima.
- `<gateway>` ⟶ IP del router/gateway.

Nel secondo terminale (comunicazione inversa):
```bash
arpspoof -i eth1 -t <gatewayIP> <targetIP>
```
Questo configura un **MITM bidirezionale**, in cui la macchina Kali si interpone tra vittima e gateway e funziona nel caso in cui la richiesta per l'autenticazione per il server SMB debba pasare per il gateway cosí da poterla intercettare e inviare la richiesta al server SMB tramite il modulo `smb_replay` in attesa.

---

## Comportamento del Relay
Tutto gira intorno al modulo `smb_replay` che rimane in attesa di una richiesta di aunteticazione NTLM da inoltrare al server SMB. <brt>
Per poter aumentare le porbabilità che la richiesta venga inviata alla macchina kali, usiamo `dnsspoof` per intercettare la richiesta dns da parte della vittima nel caso in cui non avesse in cache l'indirizzo IP del server SMB. <br>
Se invece il server SMB si dovesse trovare fuori dalla sottorete della macchina vittima, la macchina vittima dovrá effettuare una richiesta verso il gateway che impersonificheremmo grazie al `arpspoof`. <br> 
Se volessimo aumentare ancora di piú le probabilitá, dovremmo configurare un `arpspoof` in modo da impersonificare il server SMB per evitare che la richiesta non venga inviata verso la macchina kali se la vittima dovesse avere già l'indirizzo IP del server SMB al momento del lancio dell'attacco. 

---
