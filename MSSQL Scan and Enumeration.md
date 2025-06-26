## Identificazione del Servizio
Il primo passo é quello di verificare che servizio é in esecuzione sulla macchina target.
### Nmap
```bash
nmap -p 1433 <IP>
```
NSE utili:
```bash
nmap -p 1433 --script ms-sql-info <IP>              # Restituisce nome dell’istanza, versione, dominio Windows, nome host
nmap -p 1433 --script ms-sql-empty-password <IP>    # Tenta accesso con password vuota.
nmap -p 1433 --script ms-sql-brute <IP>             # Esegue un attacco brute force alle credenziali SQL Server Authentication
nmap -p 1433 --script ms-sql-xp-cmdshell <IP>       # Verifica se la funzione xp_cmdshell è abilitata
nmap -sU -p 1434 --script ms-sql-info <IP>          # Utile quando il SQL Browser è attivo su UDP
```
### MEtasploit
```bash
use auxiliary/scanner/mssql/mssql_ping   # Verifica la presenza del servizio via UDP/1434
use auxiliary/scanner/mssql/mssql_login  # Test credenziali SQL Server Authentication
use auxiliary/admin/mssql/mssql_enum     # Enumera database, tabelle e configurazioni.
```
Dopo aver ottenuto informazioni su versione, e probabili sessioni senza credenziali, possiamo procedere con la fase di Exploiting.
