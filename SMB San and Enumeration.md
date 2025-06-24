<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
## Identificazione del Servizio
Il primo passo é quello di verificare se servizio é in esecuzione sulla macchina target.

```bash
nmap -sV -p139,445 <target>
```
### NSE utili
```bash
nmap --script smb-os-discovery <target>         # sistema operativo, dominio, NetBIOS
nmap --script smb-enum-shares <target>          # cartelle condivise disponibili
nmap --script smb-enum-users <target>           # utenti configurati
nmap -p445 --script smb-vuln-ms17-010 <IP>      # verifica EternalBlue
nmap -p445 --script=smb-protocols <IP>          # protocolli supportati (NTLM, LM, SMB1/2/3)
nmap -p445 --script=smb-security-mode <IP>      # modalità di sicurezza attiva (autenticazione, accessi anonimi, ecc.)
```
> se gli nse dovessero dare un accesso negato, é necessario passare le credenziali come argomento dello script con <br>`--script-args smbuser=<utente>,smbpass=<password>`
### Metasploit
```bash
use auxiliary/scanner/smb/smb_version         # verifica versione SMB
use auxiliary/scanner/smb/smb_enum_users      # enumerazione utenti
use auxiliary/scanner/smb/smb_enumshares      # enumerazione condivisioni
use auxiliary/scanner/smb/smb_ms17_010        # verifica vulnerabilità EternalBlue
```

---

## Verifica Null Session

Una **null session** è una connessione SMB senza credenziali, potenzialmente utile per ottenere:
- Nomi utenti
- Share
- Informazioni di dominio
```bash
rpcclient -U "" <target>
smbclient -L //<target>/ -N
```

---

## Altri modi
esistono altri metodi per tentare di enumerare senza credenziali, ma solo se il server é configurato in modo da permettere gli accessi anonimi
```bash
enum4linux -a <IP>
crackmapexec smb <target> --shares   # elenca condivisioni
crackmapexec smb <target> --users    # enumera utenti
```
### smbclient
Client per connettersi e interagire con le share:
```bash
smbclient -L //<target>/ -N # condivisioni anonime
smbclient -L <IP> -U <username> # share disponibili per utente
smbclient //<target>/<share> -U <username> # accesso alla share
```
Comandi interni:
- `?`, `ls`, `get <file>`, `put <file>`, `cd`, `mkdir`, `rmdir`
> File come `*.tar.gz` possono essere estratti con `tar xzf file.tar.gz`
