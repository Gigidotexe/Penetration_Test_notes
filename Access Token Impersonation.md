# Access Token Impersonation

## Cos'è un Access Token in Windows?

Gli **Access Token** in Windows sono elementi fondamentali del meccanismo di autenticazione e autorizzazione. <br>
Vengono generati dal **LSASS** (Local Security Authority Subsystem Service), processo visibile nel Task Manager come `lsass.exe`.

Un token di accesso può essere immaginato come una "chiave temporanea" che identifica l’utente autenticato e definisce i suoi privilegi. È simile ai cookie nel web: una volta autenticato, il token viene associato a ogni processo dell’utente per accedere a risorse e servizi di sistema, evitando di inserire continuamente le credenziali.

Quando un utente effettua un login:  
- `Winlogon.exe`, che gestisce l'interfaccia di logon, passa le credenziali che l'utente ha inserito a `LSASS.exe`. <br>
- `LSASS.exe` controlla le credenziali e se sono corrette crea una struttura temporanea che rappresenta il profilo utente (inclusi SID, gruppi, privilegi) e chiede al kernel tramite SRM (Security Reference Monitor) di creare un Access Token.
- `SRM` nel kernel, costruisce l’Access Token vero e proprio, associandolo a un oggetto che vive nello spazio di memoria del kernel <br>(come `PROCESS` object, `THREAD` objects, eccetera).
- `Winlogon` riceve la conferma e l'access token per l'utente autenticato. Da questo momento, tutti i processi vhe verranno avviati dall'utente utilizzeranno questo token. Quindi `Winlogon` lancia `userinit.exe`.
- `userinit.exe` erediterà il token utente e avvia `explorer.exe` che é il processo centrale del desktop environment di Windows che gestisce l’intero desktop, la barra delle applicazioni (taskbar), il menu Start, le tray icons (notifiche accanto all’orologio), e l’interfaccia utente generale

Tutti i processi figli creati da `userinit` erediteranno il token, eseguendosi con i medesimi privilegi.  
Esempio: se un utente apre il prompt dei comandi, questo verrà eseguito con il token associato al suo login.

I token possono essere di due tipi principali:

- **Token Delegate**: creati durante login interattivi (come accesso fisico o via RDP). Permettono accesso completo a risorse remote.
- **Token Impersonate**: generati da servizi per eseguire operazioni a nome dell’utente. Consentono accesso limitato, spesso locale.

## Differenze tra Token Delegate e Impersonate

- Un **Delegate Token** consente l’utilizzo di credenziali complete, permettendo l’accesso a risorse di rete. È quindi più pericoloso in caso di attacchi.
- Un **Impersonate Token** consente di impersonare localmente un utente, ma non può essere usato per connessioni remote.

## Privilegi necessari per l’Access Token Impersonation

Alcuni privilegi devono essere presenti per attuare questa tecnica:
```bash
SeAssignPrimaryTokenPrivilege      # consente di assegnare token primari ai processi.
SeCreateTokenPrivilege             # permette di creare token arbitrari.
SeImpersonatePrivilege             # abilita la creazione di processi con il contesto di sicurezza di un altro utente.
```
é possibile verificare i permessi attuali dell'utente con:
```bash
getprivs                           # elenca i privilegi dell'utente attuale
```

## Uso del modulo Incognito in Meterpreter

`Incognito` è un’estensione integrata in Meterpreter (in passato standalone) che permette di elencare e impersonare token di accesso disponibili nella memoria del sistema.

### Fasi pratiche
Dopo aver ottenuto una sessione meterpeter non amministrativa, possiamo procedere con i pasaggi per poter tentare di impersonificare un token e ottenere elevati privileti
```bash
getprivs                    # e cerchiamo SeImpersonatePrivilege
load incognito              # carica il modulo Incognito nella sessione Meterpeter
list_tokens -u              # comando incognito per mostrare la lista di token disponibili per l'impersonificazione
impersonate_token "<TOKEN>" # impersonifica il token selezionato
getuid
```
Dopo aver impersonificato un token é necessario migrare la sessione meterpeter in un altro processo appartenente ad un utente con privilegi elevati perché l'attuale sessione avrá ancora il token iniziale associato (con pochi privilegi).

Se possibile, migrare nel processo `explorer.exe` perché è quasi sempre il processo principale con un Access Token primario completo dell’utente interattivo autenticato: <br>
```bash
pgrep explorer   # individua il PID del processo explorer.exe
migrate <PID>    # tenta di migrare la sessione nel processo explorer
```
> Questa migrazione non è sempre possibile con utenti limitati.

Dovremmo aver ottenuto una sessione meterpeter con elevati privilegi 
```bash
getprivs         # verifica i privilegi disponibili dopo l’impersonificazione
```
Possiamo anche rieseguire `list_tokens -u` per vedere se possiamo impersonificarere altri token con privilegi piú alti.

#### Nota sull’importanza del privilegio SeImpersonatePrivilege
Senza il privilegio `SeImpersonatePrivilege`, non è possibile completare questa tecnica. È essenziale che l’utente attaccato ne disponga. <br>
> E se non ci sono token disponibili?

Quando non ci sono token disponibili da impersonare, possiamo usare tecniche alternative come l’**attacco Potato**, che sfrutta vulnerabilità o errate configurazioni nei privilegi di rete e pipe di sistema.
