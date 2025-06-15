# Privilege Escalation - Introduzione

Il privilege escalation (escalation dei privilegi) è il processo attraverso il quale un attaccante, una volta ottenuto l’accesso a un sistema, cerca di acquisire **privilegi superiori**, passando ad esempio da un utente normale a un utente amministratore (`Administrator` su Windows o `root` su Linux).

Questo processo può avvenire in due forme principali:

- **Vertical Privilege Escalation**: quando si sale da un utente con pochi privilegi a un account privilegiato.
- **Horizontal Privilege Escalation**: quando si passa da un utente a un altro utente con pari privilegi, ma con accesso a risorse differenti.

## Perché è così importante?

La privilege escalation è una **fase cruciale** in qualsiasi penetration test, perché consente all’attaccante di:

- Accedere a informazioni e file riservati
- Installare strumenti di post-exploitation
- Spostarsi lateralmente su altri host nella rete
- Impostare backdoor persistenti
- Disattivare meccanismi di sicurezza

È un indicatore molto importante anche per chi difende: **se l’attaccante non riesce a eseguire l’escalation, significa che il sistema ha una configurazione sicura e robusta**. Al contrario, se è possibile salire di livello con facilità, l’organizzazione è vulnerabile a compromissioni gravi.

Anche se tecnicamente **non è obbligatoria per completare un attacco**, il suo successo determina spesso **l’impatto finale** dell’intrusione.

## Come si ottiene la privilege escalation?

L’escalation dei privilegi si ottiene sfruttando:

- **Vulnerabilità locali** (es. exploit nel kernel, privilege escalation exploits)
- **Errori di configurazione** (servizi con permessi troppo permissivi, file eseguibili modificabili, script mal configurati)
- **Servizi in esecuzione con privilegi elevati** accessibili da utenti limitati
- **Password memorizzate in chiaro** nei file di configurazione o script
- **Token o handle di processo duplicabili** (su Windows)

Ad esempio, in ambiente Windows si possono cercare:

- Processi che girano come SYSTEM
- DLL hijacking
- Scheduled tasks vulnerabili
- Servizi configurati con binari scrivibili da utenti non privilegiati

In ambiente Linux, l’enumerazione dei comandi `sudo`, dei permessi dei file e dei SUID/SGID è spesso il punto di partenza.

## Gli exploit e gli strumenti

Esistono **exploit pubblici** per centinaia di vulnerabilità note che permettono di ottenere escalation dei privilegi in determinati ambienti e versioni di sistema operativo. Uno dei più noti è `MS16-032` su Windows o `DirtyCow` su Linux.

Strumenti fondamentali per automatizzare la ricerca delle vulnerabilità includono:

- `LinPEAS`, `Linux Exploit Suggester` su Linux
- `WinPEAS`, `Seatbelt`, `PowerUp`, `Sherlock` su Windows

Questi tool aiutano il pentester a identificare rapidamente i vettori di escalation possibili, evidenziando problemi come servizi con permessi errati, software obsoleti, chiavi di registro mal configurate o processi vulnerabili.
