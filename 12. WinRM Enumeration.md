# WinRM Enumeration

WinRM (Windows Remote Management) è un protocollo Microsoft che consente l’amministrazione remota di macchine Windows tramite PowerShell. È basato su HTTP e HTTPS, e le sue porte predefinite sono:

- `5985/TCP` per HTTP
- `5986/TCP` per HTTPS

WinRM viene usato in ambienti Windows moderni, soprattutto in Active Directory, per gestire sistemi da remoto. Se correttamente configurato, può essere un punto d’accesso molto potente per un attaccante.

## Perché è utile durante un penetration test?

WinRM permette l’esecuzione remota di comandi PowerShell in maniera autenticata. Se hai delle **credenziali valide di un utente Windows**, puoi usarle per:

- Ottenere una shell remota
- Eseguire comandi da terminale
- Trasferire file, modificare configurazioni, fare enumeration

È uno dei metodi principali di **lateral movement** in ambienti enterprise Windows.

## Fase 1 – Scansione iniziale

Puoi usare Nmap per rilevare WinRM:

`nmap -p 5985,5986 -sV <IP>`

Per maggiori informazioni:

`nmap -p 5985 --script http-winrm-info <IP>`

Questo ti dice se il servizio è attivo e, in alcuni casi, ti restituisce dettagli sulla versione, autenticazione richiesta, header, certificati.

## Fase 2 – Connessione con credenziali valide

### Evil-WinRM (tool fondamentale)

Evil-WinRM è lo strumento più usato per interagire con WinRM durante un pentest. Se hai una coppia utente/password o utente/hash, puoi connetterti così:

`evil-winrm -i <IP> -u <utente> -p <password>`

Esempio:

`evil-winrm -i 192.168.1.10 -u administrator -p Spring2023!`

Una volta dentro, hai una shell PowerShell remota. Puoi eseguire comandi, caricare file, e anche avviare script di escalation.

### Altri parametri utili:

- `-s`: directory con script personalizzati
- `-e`: directory con eseguibili
- `-c`: directory con certificati

## Fase 3 – Enumerazione interna via WinRM

Una volta ottenuto l’accesso, puoi eseguire molti comandi di ricognizione:

- `whoami` → conferma l’identità dell’utente
- `hostname` → nome della macchina
- `ipconfig` → interfacce di rete
- `net users` / `net localgroup administrators` → utenti e privilegi
- `systeminfo` → patch, OS, build

PowerShell ti consente di fare tutto quello che potresti fare da terminale locale, ma da remoto.

## Fase 4 – Accesso con hash (pass-the-hash)

Evil-WinRM supporta anche l’uso di hash NTLM:

`evil-winrm -i <IP> -u <utente> -H <hash>`

Questa tecnica è molto utile se hai ottenuto l’hash da SMB, LSASS o dump del sistema.

## Fase 5 – Enumerazione con Metasploit

Esiste anche un modulo in Metasploit:

`use auxiliary/scanner/winrm/winrm_login` <br>
`set RHOSTS <IP>` <br>
`set USERNAME <utente>` <br>
`set PASS_FILE <wordlist>` <br>
`run` <br>

Funziona in modo simile a un bruteforce.

## Considerazioni di sicurezza

- WinRM è sicuro solo se protetto da firewall e accesso ristretto agli host autorizzati.
- In molti ambienti, l’accesso è consentito solo da macchine del dominio.
- Evil-WinRM è rilevabile da alcuni antivirus/EDR se usato senza cautela.

## Conclusione

WinRM non è sfruttabile senza credenziali, ma **diventa una porta d’ingresso molto potente** quando ottieni accesso. Ti permette di entrare con PowerShell in una macchina remota Windows e di fare tutto, dal dumping dei processi all’esecuzione di script per l’escalation.

Se stai lavorando in un contesto Windows e trovi la porta `5985` o `5986` aperta, segnala WinRM come un punto chiave per il movimento laterale o l’accesso remoto.
