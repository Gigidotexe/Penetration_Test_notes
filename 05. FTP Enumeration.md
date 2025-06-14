
# FTP Enumeration

Il protocollo FTP (File Transfer Protocol) è uno dei servizi più comuni e storicamente più vulnerabili, usato per trasferire file tra client e server. È semplice, ma presenta diversi punti deboli, soprattutto in configurazioni legacy o poco sicure. La porta predefinita è la `21/TCP`, ma può usare anche la `20` per il trasferimento dati.

Durante una fase di penetration test, l’enumerazione FTP è fondamentale per determinare:

- Se è possibile effettuare un **login anonimo**.
- Quali directory e file sono accessibili.
- Quali permessi sono associati a ciascun file o cartella.
- Se è presente un **banner** che riveli la versione del server FTP.
- Se vi sono directory world-readable o writeable.

## Connessione al servizio

Per verificare manualmente la disponibilità di un servizio FTP e tentare un login, si può usare:

- `ftp <IP>` oppure
- `nc <IP> 21` per leggere il banner.

Una volta connessi, molti server FTP accettano login con:

- `Username: anonymous`
- `Password: <vuoto>` oppure un’email qualunque

Se il login ha successo, significa che il server consente **accesso anonimo**, che può portare all'esfiltrazione di dati, file di configurazione, backup o script sensibili.

## Enumerazione manuale

All’interno della sessione FTP:

- `ls` o `dir`: elencano file e directory.
- `cd <directory>`: naviga nelle directory.
- `get <file>` / `mget *`: scarica uno o più file.
- `put <file>` / `mput *`: carica file (se permesso).
- `pwd`: mostra la directory corrente.
- `binary` / `ascii`: cambia la modalità di trasferimento.

È importante verificare se è possibile scrivere (upload) o eseguire file `.php`, `.asp` o `.cgi` in directory accessibili via web.

## Banner grabbing

Per identificare la versione del servizio FTP, basta connettersi in telnet o netcat:

`nc <target> 21`

Il server spesso restituisce un banner simile a:

`220 (vsFTPd 2.3.4)`

Questa informazione è fondamentale per cercare vulnerabilità note. Ad esempio, `vsFTPd 2.3.4` ha una backdoor famosa che consente l’accesso remoto con payload.

## Strumenti automatizzati

### Nmap

Il modulo NSE `ftp-anon` consente di verificare automaticamente l’accesso anonimo:

`nmap -p 21 --script=ftp-anon <IP>`

Per rilevare la versione:

`nmap -sV -p 21 <IP>`

Altri script utili:

- `ftp-bounce`: verifica vulnerabilità FTP bounce (obsoleta ma ancora presente in vecchi server).
- `ftp-syst`: raccoglie informazioni sul sistema remoto.
- `ftp-proftpd-backdoor`: specifico per versioni vulnerabili di ProFTPD.

### Hydra

Per il brute-force:

`hydra -l <user> -P <wordlist> ftp://<IP>`

### Metasploit

Modulo per login anonimo:

`use auxiliary/scanner/ftp/anonymous`

Modulo per brute-force:

`use auxiliary/scanner/ftp/ftp_login`

Esempio di uso:

`set RHOSTS <IP>`
`set USERNAME anonymous`
`run`

## Permessi e vulnerabilità

Verifica se è possibile:

- Scrivere in directory leggibili via web (RFI, LFI, upload webshell).
- Sovrascrivere file esistenti.
- Caricare file `.exe`, `.php`, `.txt` sospetti.
- Abusare di script automatici che leggono da directory FTP (cron, watcher, trigger, ecc.).

Alcuni server esposti su internet lasciano accesso pubblico a `ftp://IP` tramite browser o strumenti come `wget`, `curl`, `lftp`.

FTP è un protocollo testuale e in chiaro: tutte le credenziali, comandi e trasferimenti passano **senza cifratura**, quindi in scenari reali è facile sniffare il traffico con Wireshark o tcpdump.

## FTP con SSL/TLS

Versioni più moderne usano FTPS (FTP over SSL) sulla porta `990`. In questi casi, la scansione e l’accesso richiedono strumenti aggiornati, come `lftp`, `openssl`, `nmap --script=ssl-*`.
