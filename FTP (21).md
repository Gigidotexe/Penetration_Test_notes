# FTP Enumeration & Exploitation

**FTP (File Transfer Protocol)** è un protocollo di rete standard utilizzato per il trasferimento di file tra client e server.  
Opera in genere sulla porta TCP **21**, e in alcuni casi sulla **20** per il trasferimento dati.  
È uno dei protocolli più semplici e longevi, ma anche tra i più vulnerabili, soprattutto in configurazioni legacy o poco sicure.

FTP supporta diverse operazioni:
- Navigazione tra directory
- Caricamento (upload) di file
- Scaricamento (download) di file
- Visualizzazione di permessi e struttura

La sua principale debolezza è che si tratta di un protocollo **testuale e in chiaro**: tutte le credenziali, i comandi e i dati viaggiano senza crittografia, esponendo l’intero flusso a possibili intercettazioni (es. tramite Wireshark o tcpdump).  
Inoltre, è frequente trovare server FTP con configurazioni errate, accessi anonimi aperti, directory sensibili non protette o vulnerabilità legate a versioni obsolete del software.

---

## Identificazione del servizio

Il primo passo in fase di enumerazione consiste nel verificare se il servizio è attivo e identificarne la versione:

```
nmap -sV -p21 <IP>
```

- `-sV` consente il banner grabbing per determinare la versione del servizio  
- `-p 21` isola la scansione alla porta FTP

Per il banner grabbing manuale:

```
nc <IP> 21
```

Un esempio di risposta del server potrebbe essere:

```
220 (vsFTPd 2.3.4)
```

Questa informazione è essenziale per identificare vulnerabilità note, come la famosa **backdoor in vsFTPd 2.3.4**.

---

## Enumerazione FTP con Metasploit

Su Metasploit esiste un modulo specifico per effettuare **enumerazione del servizio FTP**, che consente di estrarre il banner e raccogliere informazioni utili:

```
use auxiliary/scanner/ftp/ftp_version
```

Questo modulo può aiutare a identificare rapidamente la versione in uso, utile per cercare successivamente exploit con:

```
search <versione>
```

---

## Accesso anonimo

Alcuni server FTP accettano l'accesso con l'utente speciale `anonymous`:

```
ftp <IP>
```

Alla richiesta di credenziali:

```
Name: anonymous
Password: [qualsiasi email o vuoto]
```

Se l'accesso ha successo, si ottiene accesso pubblico ai file configurati come world-readable. Tuttavia, se il server restituisce:

```
530 Login incorrect.
Login failed.
Remote system type is UNIX.
Using binary mode to transfer files.
```

significa che l'accesso anonimo è disabilitato.

Per automatizzare la verifica dell'accesso anonimo:

```
nmap -p 21 --script=ftp-anon <IP>
```

---

## Esplorazione manuale delle directory

Comandi utili all'interno di una sessione FTP:

- `ls` o `dir` → elencano directory e file  
- `cd <directory>` → cambia directory  
- `get <file>` / `mget *` → scarica uno o più file  
- `put <file>` / `mput *` → carica file (se permesso)  
- `pwd` → mostra la directory corrente  
- `binary` / `ascii` → modalità di trasferimento

Verifica sempre se è possibile caricare file `.php`, `.asp`, `.cgi` in cartelle servite da un web server: questo scenario può portare all'esecuzione di una **webshell** (al path `/usr/share/webshells/`).

---

## Brute force delle credenziali

Per tentare un brute force delle credenziali:

```
hydra -L <usernameList> -P <passwordList> <IP> -t 4 ftp
```

- `-L` specifica la lista di username  
- `-P` la wordlist delle password  
- `-t 4` imposta il numero di thread

Se le credenziali sono corrette, Hydra lo notificherà a schermo.

---

## Connessione con credenziali valide

Dopo aver scoperto una coppia `user:pass`, accedi con:

```
ftp <IP>
```

Poi:

```
Name: <username>
Password: <password>
```

All'interno della sessione, utilizza i comandi visti sopra per navigare, scaricare o caricare file.

---

## Ricerca exploit noti

Se conosci la versione del server FTP, puoi verificare vulnerabilità note con:

```
searchsploit <ftp-versione>
```

Per poi copiarne il path e creare una copia locale:

```
searchsploit -m <path>
```

Esempio:

```
searchsploit vsftpd 2.3.4
```

---

## Strumenti automatizzati

### Metasploit

Modulo per verificare l'accesso anonimo:

```
use auxiliary/scanner/ftp/anonymous
```

Modulo per brute force:

```
use auxiliary/scanner/ftp/ftp_login
set RHOSTS <IP>
set USERNAME anonymous
run
```

Modulo per enumerazione versione (già visto sopra):

```
use auxiliary/scanner/ftp/ftp_version
```

### Nmap NSE Scripts

- `ftp-anon` → verifica l'accesso anonimo  
- `ftp-bounce` → verifica la vulnerabilità FTP bounce (obsoleta)  
- `ftp-syst` → raccoglie informazioni di sistema  
- `ftp-proftpd-backdoor` → verifica exploit in ProFTPD  

---

## Permessi e attacchi post-accesso

Verifica sempre:

- Permessi di scrittura in cartelle world-readable  
- Possibilità di caricare file sospetti (`.php`, `.exe`, `.txt`, ecc.)  
- Cartelle sorvegliate da cronjob o watcher (trigger automatici)

In alcuni casi è possibile accedere direttamente via browser:

```
ftp://<IP>
```

Oppure usare strumenti alternativi:

```
wget ftp://<IP>
curl ftp://<IP>
lftp <IP>
```

---

## FTP con SSL/TLS (FTPS)

Versioni moderne di FTP utilizzano SSL/TLS sulla porta `990` (implicit FTPS).

Per identificare e interagire con questi server:

```
nmap -sV -p 990 --script=ssl-* <IP>
```

Oppure:

```
openssl s_client -connect <IP>:990
```

Per connettersi:

```
lftp -u <user>,<pass> ftps://<IP>
```
