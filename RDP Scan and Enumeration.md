## Identificazione del Servizio
Il primo passo é quello di verificare che servizio é in esecuzione sulla macchina target.
### Nmap
Per verificare la presenza del servizio RDP:
```bash
nmap -p 3389 -sV <IP>
```
Per ottenere informazioni aggiuntive tramite NTLM:
```bash
nmap -p 3389 --script rdp-ntlm-info <IP>
```
Questo script può restituire:
- Nome del dominio
- Nome macchina
- Versione del sistema operativo
Questi dati possono essere utili anche senza autenticazione.
> É possibile che la porta per RDP venga spostata quindi si consiglia di eseguire una scansione su tutte le porte (`-p-`) per identificarla e successivamente modificare la porta `3389` con quella nuova.

Se si tratta di vecchie versioni di RDP, vedere se il sistema é vulnerabile a delle vulnerabilitá note
```bash
nmap -p 3389 --script rdp-vuln-ms12-020 <IP>         # verifica vulnerabilitá BlueKeep
nmap -p 3389 --script rdp-vuln-cve2019-0708 <IP>     # Verifica piú specifica per BlueKeep
```
### Metasploit
```bash
use auxiliary/scanner/rdp/rdp_scanner
set RHOSTS <IP>
set RPORT <PORT>
run
```

---

## Connessione Manuale
Tentativi di connessione con:
- Linux: `xfreerdp /v:<IP>`
- Kali: `rdesktop <IP>`
- Windows: `mstsc.exe`
> Se l'accesso é portetto da password, passare alla fase di Exploiting

Osservazioni da fare:
- Prompt di login
- Banner informativi
- Errori che rivelano dettagli su utenti, domini, o configurazioni

Esempi di messaggi utili:
- “Utente non autorizzato” ⟶ utente esiste ma non ha accesso remoto
- “Username o password errata” ⟶ username valido
- “Nessun dominio specificato” ⟶ server non è parte di un dominio

