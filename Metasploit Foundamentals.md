
## MSF Database (MSFDB)

**MSFDB** è il database interno di Metasploit usato per memorizzare tutti i dati generati durante il penetration test, come risultati di scansione, vulnerabilità identificate, host scoperti, ecc. 
È basato su **PostgreSQL** e ne richiede l’esecuzione e la corretta configurazione per funzionare. 

Il database facilita anche l’importazione dei dati provenienti da altri strumenti di terze parti come **Nmap** o **Nessus**.

```bash
sudo msfdb init       # per inizializzarlo
sudo msfdb reinit     # in caso di problemi
sudo msfdb status     # per verificarne lo stato
db_status             # per verificarne lo stato dentro msfconsole
```
> puoi usare anche `service postgresql start` per avviarlo.
---

## Gestione dei Workspace

In Metasploit, un **workspace** è un ambiente isolato dove vengono memorizzati tutti i dati raccolti durante un test, come host, vulnerabilità, credenziali e risultati delle scansioni. 
Il workspace di default è chiamato `default`, ma è altamente consigliato creare un workspace separato per ogni test o cliente.
> Tutti i dati dei workspace sono memorizzati dentro al **MSFDB**.

Per visualizzare la documentazione del comando:
```bash
workspace -h
```
Comandi principali:
```bash
workspace                        # mostra il workspace attuale e quelli disponibili
workspace -a <nome>              # crea un nuovo workspace e ci sposta dentro
workspace <nome>                 # cambia workspace
workspace -d <nome>              # elimina un workspace
workspace -r <vecchio> <nuovo>   # rinomina un workspace
```

Comando utile:
```bash
hosts                     # mostra gli host salvati nel database del workspace corrente
analyze                   # analizza degli exploit applicabili ad un host memorizzato
sessions                  # Per monitorare le sessioni attive
sessions -i <ID_sessione> # per interagire con una specifica sessione
```

---

## Uso Pratico

Per poter utilizzare qualsiasi modulo in Metasploit, è essenziale **impostare correttamente le variabili richieste**, tra cui:
```bash
set LHOST   # IP su cui ricevere i dati
set LPORT   # porta su cui l’attaccante riceve la connessione dal target
set RHOST   # IP del target
set RHOSTS  # elenco di IP da attaccare (utile per attacchi multipli)
set RPORT   # porta del servizio da attaccare sul target
```
> Comandi utili: <br>
> `Ctrl+L` per pulire il terminale <br>
> `help` per visualizzare i comandi disponibili


Per cercare moduli o funzionalità:
```bash
search <parola_chiave>         # esempio: search portscan
search type:exploit nam:ssh    # filtri per la ricerca
use <nome_modulo>              # <nome_modulo> o <codice> primma del nome del modulo
show options                   # visualizzare le opzioni richieste da un modulo
unset RHOST                    # per rimuovere una variabile
run                            # (oppure "exploit") per avviare un modulo
show payloads                  # per visualizzare i payload disponibili
set payload <nome_payload>     # impostare un payload
```

---

## Nmap in Metasploit

Metasploit permette di importare ede eseguire scansioni nmap per poter avere tutti i rultati nell database in modo tale da porter repreire tutti i dati usando
```bash
hosts       # lista degli host scoperti
services    # porte, protocolli e servizi rilevati
vulns       # vulnerabilità associate agli host
```

### Importazione delle scansioni 
dopo aver eseguito una scansione Nmap, possiamo generare un file di output contenente i risultati in formato XML per poi importarlo dentro il database di Metasploit:
```bash
nmap <IP> -oX output.xml      # gera file xml
db_import <path_output.xml>   # importa in msfconsole
```
### Scansione interna via db_nmap
possiamo limitare il numero di passaggi eseguendo direttamente la scansione dentro Metasploit, i dati verranno salvati automaticamente nel database:
```bash
db_nmap -sV -Pn 192.168.1.0/24
```

---
