
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
