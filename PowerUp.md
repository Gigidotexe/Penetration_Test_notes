# PowerUp 

É uno dei moduli più usati di <a href="https://github.com/PowerShellMafia/PowerSploit">PowerSploit</a>, accessibile su <a href="https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1">GitHub</a>

PowerSploit é un framework di post-exploitation scritto in PowerShell, sviluppato per aiutare i penetration tester e red teamer a interagire con ambienti Windows compromessi.
Il progetto è suddiviso in più moduli, ciascuno dei quali focalizzato su una fase della post-exploitation:
- Recon (ricognizione)
- Privesc (privilege escalation)
- Exfiltration (esfiltrazione dati)
- Persistence (persistenza)
- CodeExecution (esecuzione codice)
- ScriptModification (modifica di script PowerShell)

PowerUp, invece, è progettato per **automatizzare la ricerca di vulnerabilità di privilege escalation** locali in un ambiente Windows compromesso. Include una serie di funzioni PowerShell che rilevano configurazioni errate o vulnerabili nei permessi, nei servizi e nei file del sistema.

Tra le funzionalità principali troviamo:

- **Get-ServiceUnquoted** ⟶ cerca servizi con percorsi non quotati.  
- **Get-ModifiableServiceFile** ⟶ individua servizi i cui file binari possono essere modificati.  
- **Get-ModifiableScheduledTaskFile** ⟶ cerca task pianificati modificabili.  
- **Invoke-AllChecks** ⟶ esegue una scansione completa per privilege escalation.  

---

## Utilizzo di PowerUp

Per utilizzare PowerUp su una macchina compromessa:

1. Scaricare lo script `PowerUp.ps1` da GitHub.
2. Trasferirlo sulla macchina target compromessa (ad esempio tramite `upload` da Meterpreter).
3. Aprire una shell PowerShell elevata (se possibile).
4. Eseguire il seguente comando per importare lo script:

`powershell` ⟶ avvia una nuova sessione della shell PowerShell <br> 
`powershell -ep bypass` ⟶ Bypassa la ExecutionPolicy <br>
`.\PowerUp.ps1` ⟶ esegue lo script PoweUp.ps1<br>
`Invoke-AllChecks` ⟶ esegue tutti i controlli di privilege escalation disponibili nello script <br>

Questo comando carica il modulo PowerUp e avvia una scansione per cercare configurazioni pericolose. I risultati includeranno eventuali:

- Servizi con binari modificabili
- DLL hijacking
- Task pianificati scrivibili
- Token e permission escalation
- UAC bypass
- Potenziali exploit locali
