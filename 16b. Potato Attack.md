# Potato Attacks (JuicyPotato, RoguePotato, PrintSpoofer)

## Cos'è l'attacco Potato?

Gli attacchi "Potato" sono una famiglia di tecniche di privilege escalation locali che sfruttano vulnerabilità nei meccanismi di comunicazione tra servizi privilegiati e utenti meno privilegiati all'interno di Windows. Il loro obiettivo è ottenere una **shell con privilegi SYSTEM** partendo da un contesto utente con privilegi limitati, ma che ha accesso a determinati servizi.

Questi attacchi si basano principalmente su:

- Named Pipes
- Token impersonation
- Servizi con configurazioni errate (es. BITS, DCOM, Print Spooler)

L’elemento fondamentale di questi attacchi è la presenza del privilegio `SeImpersonatePrivilege`, che permette all’utente di impersonare un token con privilegi più elevati una volta che il sistema glielo consente tramite canali non sicuri.

## Le varianti principali:

### JuicyPotato

JuicyPotato sfrutta la vulnerabilità legata al processo DCOM in combinazione con la creazione di named pipe che intercettano token ad alto privilegio da servizi locali. Supporta solo Windows 7, 8, Server 2008/2012.

### RoguePotato

Funziona in ambienti più moderni come Windows 10, 2016, 2019 e risolve alcune limitazioni di JuicyPotato. Utilizza COM Server Registration per forzare la comunicazione con un servizio che gira come SYSTEM e ottenere un token.

### PrintSpoofer

È una tecnica più recente e molto affidabile su sistemi con Print Spooler attivo. Utilizza l’API `ImpersonateNamedPipeClient` e il servizio `PrintSpoofer` per ottenere un token SYSTEM da impersonare. È stato patchato nelle versioni più recenti di Windows 10 e 11.

## Requisiti fondamentali

- `SeImpersonatePrivilege` deve essere presente. <br>
- La macchina non deve essere completamente aggiornata (molte patch disabilitano queste tecniche). <br>
- È necessario avere un binario dell'exploit da caricare sulla macchina target. <br>

## Come eseguire l’attacco con PrintSpoofer (esempio)

1. Scarica PrintSpoofer da GitHub: https://github.com/itm4n/PrintSpoofer
2. Trasferisci il binario sulla macchina target (es. nella cartella `C:\\Temp`).
3. Assicurati di avere una sessione Meterpreter attiva.

### Passaggi:

`cd C:\\` ⟶ vai alla root del sistema. <br>
`mkdir Temp` ⟶ crea una cartella temporanea. <br>
`cd Temp` ⟶ entra nella cartella. <br>
`upload <PrintSpoofer.exe>` ⟶ trasferisci il binario sul target. <br>
`shell` ⟶ apri una shell CMD. <br>
`PrintSpoofer.exe -i -c cmd.exe` ⟶ lancia cmd.exe come SYSTEM tramite impersonificazione. <br>

A questo punto otterremo una shell CMD con privilegi elevati. Verifica con:

`whoami` ⟶ dovresti essere `NT AUTHORITY\\SYSTEM`. <br>

---

### Importante

Molti di questi attacchi oggi sono mitigati dalle patch di sicurezza Microsoft. Tuttavia, su macchine legacy o male configurate, sono ancora estremamente efficaci. È consigliato provarli sempre in fase di escalation, specialmente se `SeImpersonatePrivilege` è disponibile ma non ci sono token impersonabili.
