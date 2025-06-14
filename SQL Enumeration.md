# MySQL Enumeration

MySQL è uno dei database più utilizzati nel mondo Linux e web. È spesso usato come backend per applicazioni dinamiche (es. WordPress, Joomla, custom CMS) e può contenere informazioni estremamente sensibili, come:

- Dati degli utenti
- Hash delle password
- Informazioni di configurazione
- Log, email, contenuti interni

La porta predefinita per MySQL è la `3306/TCP`.

## Perché enumerare MySQL durante un penetration test?

Se MySQL è esposto alla rete e accetta connessioni da host remoti, può diventare una fonte molto ricca di informazioni. Anche senza accesso diretto, la sola enumerazione può rivelare:

- Versione del server (e relative CVE)
- Utenti esistenti
- Tabelle presenti
- Permessi configurati
- Dati interni utili a escalation o pivoting

## Fase 1 – Scansione iniziale

Con Nmap puoi rilevare la presenza di MySQL e tentare una prima enumerazione:

`nmap -p 3306 -sV <IP>`
`nmap -p 3306 --script mysql-info <IP>`

Il secondo comando può restituire informazioni come:

- Versione esatta (es. `5.7.33`)
- OS host
- Metodo di autenticazione

Questo è utile per capire se la versione è vulnerabile (es. CVE-2012-2122 → bypass autenticazione).

## Fase 2 – Accesso con credenziali

Se hai scoperto o indovinato delle credenziali (es. da bruteforce, file di config trovati, info leakage), puoi connetterti da terminale con:

`mysql -h <IP> -u <utente> -p`

Esempio:

`mysql -h 192.168.1.100 -u root -p`

Se il login ha successo, accedi direttamente alla CLI del database.

## Fase 3 – Enumerazione interna (post-login)

Una volta dentro, alcuni comandi fondamentali:

- `SHOW DATABASES;` → elenca tutti i database
- `USE <db>;` → entra nel database
- `SHOW TABLES;` → elenca tutte le tabelle
- `DESCRIBE <tabella>;` → mostra la struttura di una tabella
- `SELECT * FROM <tabella>;` → legge il contenuto

Spesso tabelle con nomi come `users`, `accounts`, `admin`, `login` contengono username e password (in chiaro o hash).

## Fase 4 – User enumeration e permessi

Per elencare tutti gli utenti:

`SELECT user, host FROM mysql.user;`

Questo comando restituisce la lista degli utenti definiti nel database e da dove possono connettersi (`localhost`, `%`, IP specifici...).

Per vedere i permessi:

`SHOW GRANTS FOR '<utente>'@'<host>';`

Esempio:

`SHOW GRANTS FOR 'admin'@'%';`

Può mostrare se l’utente ha permessi come `ALL PRIVILEGES`, `FILE`, `EXECUTE`...

## Fase 5 – Brute-force e tool automatici

### Hydra

Per testare credenziali deboli:

`hydra -L users.txt -P pass.txt mysql://<IP>`

### Metasploit

`use auxiliary/scanner/mysql/mysql_login`
`set RHOSTS <IP>`
`set USERNAME root`
`set PASS_FILE /usr/share/wordlists/rockyou.txt`
`run`

### mysql_enum

Script NSE utile:

`nmap -p 3306 --script mysql-enum <IP>`

### sqlmap

Se trovi una web app che usa MySQL, sqlmap può sfruttare SQL injection per ottenere shell SQL:

`sqlmap -u "http://<site>/?id=1" --dbms=mysql --dump`

## Fase 6 – Escalation o pivot

Se hai accesso come utente con permessi elevati:

- Puoi leggere file locali con `LOAD_FILE('/etc/passwd')`
- Puoi scrivere file sul filesystem (webshell!) con `SELECT ... INTO OUTFILE`

Attenzione: queste tecniche funzionano solo se l’utente ha permessi come `FILE`.

## Considerazioni finali

Un database MySQL esposto è come un diario di bordo della webapp. Se riesci a leggerlo:

- Puoi estrarre dati sensibili (es. utenti registrati)
- Puoi rubare hash delle password (da crackare con `john` o `hashcat`)
- Puoi capire la struttura della rete (nomi host, IP, log)

Nel pentesting reale, avere accesso al DB è spesso un punto di svolta: ti apre la strada a login secondari, escalation o persino RCE tramite dati abusati.
