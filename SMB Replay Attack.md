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

Dopo la configurazione si esegue l’exploit per avviare l’attacco.

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

Questo avvia il processo di DNS Spoofing sulla rete.

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
Questo configura un **MITM bidirezionale**, in cui la macchina Kali si interpone tra vittima e gateway.

---

## Comportamento del Relay

Ogni volta che la vittima tenta di connettersi a un dominio SMB (es. `\sportsfoo.com`), il DNS spoof redireziona la richiesta verso la macchina Kali. 

Il modulo Metasploit `smb_relay` intercetta la connessione SMB, acquisisce l’hash NTLM, lo inoltra al server SMB configurato e se l’attacco ha successo, si ottiene una sessione `Meterpreter` sul sistema bersaglio.

Questa catena di attacco è efficace **senza dover craccare l’hash NTLM**, sfruttando semplicemente la fiducia implicita tra sistemi sulla rete.

---
