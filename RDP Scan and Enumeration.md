<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
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

### Metasploit
```bash
use auxiliary/scanner/rdp/rdp_scanner
set RHOSTS <IP>
set RPORT <PORT>
run
```

Se si tratta di vecchie versioni di RDP, vedere se il sistema é vulnerabile a delle vulnerabilitá note
con Nmap:
```bash
nmap -p 3389 --script rdp-vuln-ms12-020 <IP>         # verifica vulnerabilitá BlueKeep
nmap -p 3389 --script rdp-vuln-cve2019-0708 <IP>     # Verifica piú specifica per BlueKeep
```
Con Metasploit:
```bash
use auxiliary/scanner/rdp/cve_2019_0708_bluekeep 
set RHOSTS <IP>  
run
```
Visualizza i target supportati:
```bash
show targets
set target <numero>
```

---

## Verifica Livello di sicurezza
rdp-sec-check é script di enumerazione RDP incluso nel progetto Impacket, progettato per verificare il livello di sicurezza configurato su un servizio RDP esposto <br>
In pratica, non tenta un accesso come fanno altri tool, ma analizza quali meccanismi di autenticazione e protezione sono abilitati sul server RDP remoto.
é scaricabile da <a href="https://github.com/fortra/impacket">GitHub</a>
```bash
cd impacket/exaples
python rdp-sec-check <IP>
```

---

## Connessione Manuale
Tentativi di connessione con:
Linux:
```bash
xfreerdp /v:<IP> /u:'' /p:'' /cert:ignore 

```
> Se sono richieste le credenziali, passa alla fase di Exploitation

```bash
sudo apt install rdesktop
rdesktop <IP>
```
Windows: 
```cmd
mstsc.exe
```
> Se l'accesso é portetto da password, passare alla fase di Exploiting

Osservazioni da fare:
- Prompt di login
- Banner informativi
- Errori che rivelano dettagli su utenti, domini, o configurazioni

Esempi di messaggi utili:
- “Utente non autorizzato” ⟶ utente esiste ma non ha accesso remoto
- “Username o password errata” ⟶ username valido
- “Nessun dominio specificato” ⟶ server non è parte di un dominio
