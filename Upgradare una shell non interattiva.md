
# Upgrading NON Interactive shell
Una shell non interattiva è una shell che viene eseguita per elaborare comandi o script senza interagire direttamente con l’utente tramite input da tastiera. <br> 
É una sorta di shell che viene eseguita in background senza dover interagire con l'utente chiedendo input e mostrando contenuti. <br> 
quando ad esempio riusciamo ad ottenere una sesione su un target linux, non viene mostrata nessuna scritta su ogni comando che eseguiamo e quello significa che ci troviamo su una shell non interattiva. <br>

Quello che possiamo fare per poterla upgradare é innanzitutto vedere che shell sono disponibili nel sistema: 
```bash
cat /etc/shells
```
tutte le shell che mostra questo comando possono essere richiamate con il path seguito dal comando `-i`. <br> 

un altro modo per poter apriire una shell é verificare che il target abbia python installato facendo il comando: 
```bahs
python --version
```
e se dovesse essere installato, possiamo eseguire: 
```bash
python -c 'import pty; pty.spawn("/bin/bash")'  # per poter ottenere una bash 
```

la stessa cosa possiamo farla con perl: 
```bash
perl -h
# se dovesse essere intallato possiamo porcedere
perl -e 'exec "/bin/bash";'
```

e con Ruby: 
```bash
ruby: exec"/bin/bash"
```

Possiamo anche farlo mostrando le variabili di ambiente: 
```bash
env
# se non dovesse esservi PATH possiamo procedere (?)
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin/:/bin
env    # per confermare 
export TERM=xterm
env
export SHELL=bash
env

```
