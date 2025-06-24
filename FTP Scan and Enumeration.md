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

Questa informazione è essenziale per identificare vulnerabilità note, come la famosa **backdoor in vsFTPd 2.3.4**. <br>
Esistono anche degli NSE per poter ottenere più informazioni sul servizio FTP: <br>
`ftp-syst` → raccoglie informazioni di sistema <br>
`ftp-anon` → verifica l'accesso anonimo <br>

---

## Accesso anonimo
Dopo aver confermato la presenza del servizio possiamo tentare di accedere con utente `anonymous` dato che alcuni server FTP accettano l'accesso questo tipo di utenza:

```
ftp <IP>
```

Alla richiesta di credenziali:

```
Name: anonymous
Password: [qualsiasi o vuoto]
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

## Enumerazione FTP con Metasploit

Su Metasploit esiste un modulo specifico per effettuare **enumerazione del servizio FTP**, che consente di estrarre il banner e raccogliere informazioni utili:

```
use auxiliary/scanner/ftp/ftp_version
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
