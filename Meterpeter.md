# Meterpreter

**Meterpreter** (Meta-Interpreter) è un **payload avanzato** incluso nel Metasploit Framework, noto per la sua versatilità e furtività. <br>
Una volta eseguito sul sistema target:

- Risiede **solo in memoria** (RAM), quindi non lascia tracce sul disco rendendosi difficile da individuare. <br>
- Permette di **eseguire comandi sul target**, **esfiltrare file**, **attivare keylogger**, **catturare screenshot**, e molto altro. <br>
- Supporta il **pivoting** e l’**escalation di privilegi**. <br>
- Può caricare script di estensione per interagire con il sistema Windows in maniera più profonda (es. dumping di hash, token impersonation). <br>
- Permette di **migrare tra processi**, chiudere e riaprire sessioni in modo dinamico. <br>

---

Quando ci troviamo in una sessione (o shell) MEterpeter, possiamo usare alcuni comandi Meterpeter per avere altre informazioni sul target
> Per avere una lista di tutti i comandi disponibili su meterpeter, scrivere `help`

```bash
sysinfo                            # mostra informazioni sul sistema target
hetuid                             # utente attuale
edit <file.txt>                    # modifica un file
download <file.txt>                # scarica il file nella directory corrente
search -d <directory> -f <file>    # cerca il file (-f) nella directory (-d)
shell                              # apre una shell sul target (ctrl+c per terminarla)
ps                                 # elenco dei processi in esecuzione
migrate <PID>                      # migra meterpeter dentro un processo
```
e molti comandi proprietari linux come `ls`, `cat` o `pwd` possono essere eseguiti dentro una sessione meterpeter

---

### Sessioni
Quando siamo in una sessione meterpeter, possiamo interagire con essa usando alcuni comandi
```bash
sessions -h          # documentazione del comando sessions
sessions             # elenca le sessioni in esecuzione
sessions -i 1        # interagisce con la sessione 1
sessions -u 1        # effettua l'upgrade della sessione ad una sessione meterpeter
sessions -n <nome>   # dai un nome alla sessione
#Dentro la sessione
background           # (oppure premere ctrl+z) mette in background la sessione attuale
exit                 # esce e killa dalla sessione attuale

```
