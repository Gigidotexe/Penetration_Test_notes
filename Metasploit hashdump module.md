
# Modulo hashdump
Su linux gli hash password vengono memorizzati dentro la directory `/etc/shadow` che puó essere visualizzato sono da un utente root. <br> 
Esiste un modulo Metasploit per poter trovare gli hash da questo file, dato che non possiamo eseguire il comando meterpreter `hashdump`, per poi petrle crackare usando il tool `John The Ripper`

```bash
use post/linux/hather/hashdump
set SESSION 1
run
```
dopo aver eseguito questo modulo, avremo tutti gli hash trovati salvati in un path che sarà visibile usando il comando `loot` su Metasploit per poi visualizzare il contenuto del file usando `cat` seguito dal path del file creato. 

Se nel file `shadow` aperto accando al nome utente é presente un asterisco (es. `root:*12345:0:99999:1:::`) significa che quel determinato user non ha una password specificata. <br> 
in questo contesto, possiamo inserire una password a quel determinato utente con il comando `passwd <username>` oppure creare un account con il comando `useradd -m <username> -s /bin/bash` e successivamente inserire due volte la password per quel account. 

Se invece dovessimo trovare una sequenza di numeri e lettere, possiamo identificare la password tramite vari passaggi. <br> 
I primi 3 caratteri in genere sono composti da un numero preceduto e seguito dal simbolo `$` e in base a quali numeri ci sono possiamo identificare il tipo di algoritmo con cui é stata hashata quella password:
- `$1$` algoritmo MD5
- `$2$` algoritmo Blowdish
- `$3$` o `$5$` algoritmo SHA-256
- `$6$` SHA-512

