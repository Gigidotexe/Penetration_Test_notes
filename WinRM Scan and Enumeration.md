<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
## Identificazione del Servizio
Il primo passo é quello di verificare che servizio é in esecuzione sulla macchina target. <br>
Potrebbe essere  che il servizio sia stato spostato in un altra porta quindi consiglio di enumerare tutte le porte del target (`-p-`).
### Nmap
La scansione standard di Nmap (`nmap -sS <IP>`) **non include automaticamente le porte 5985 e 5986**, quindi è fondamentale specificarle manualmente:
```bash
nmap -p 5985,5986 -sV <IP>
nmap -p 5985 --script http-winrm-info <IP> # script specifico per WinRM
```

### Metasploit
```bash
use auxiliary/scanner/winrm/winrm_auth_methods
set RHOSTS <IP>
run
```
> Questo modulo consente di determinare i metodi di autenticazione supportati (NTLM, Basic, Kerberos).

---

