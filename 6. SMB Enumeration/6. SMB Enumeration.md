# SMB Enumeration

SMB (Server Message Block) è un protocollo di rete usato principalmente nei sistemi Windows per la condivisione di file, stampanti e altre risorse. Le porte predefinite sono `445/TCP` (SMB moderno) e `139/TCP` (NetBIOS/SMB legacy). SMB è spesso abilitato per default sulle reti aziendali, il che lo rende un bersaglio prezioso durante un penetration test.

## Obiettivi dell'enumerazione SMB

Durante la fase di raccolta informazioni su SMB, vogliamo determinare:

- Se ci sono **condivisioni accessibili senza autenticazione** (null session).
- Quali **utenti e gruppi** esistono sul dominio.
- Quali **file o directory** sono leggibili o scrivibili.
- La **versione del servizio SMB**, utile per identificare vulnerabilità note (es. EternalBlue su SMBv1).
- Informazioni sulla **configurazione del dominio**, hostname, OS e NetBIOS name.

## Verifica della porta

Per verificare se SMB è in ascolto:

- `nmap -p 139,445 <target>`
- `nmap -sV -p 445 <target>` (rilevamento versione)

Per script NSE dedicati:

- `nmap --script smb-os-discovery <target>` → identifica OS, dominio, NetBIOS.
- `nmap --script smb-enum-shares <target>` → elenca le share.
- `nmap --script smb-enum-users <target>` → prova a elencare utenti.

## Enumerazione manuale

### smbclient

È uno strumento simile a un client FTP per SMB:

- `smbclient -L //<target>/ -N` → lista delle condivisioni disponibili senza autenticazione (`-N` = no password).
- `smbclient //<target>/<share> -N` → accede alla share se possibile.

Una volta dentro una condivisione, si possono usare:

- `ls`: lista i file.
- `get <file>`: scarica un file.
- `put <file>`: carica un file (se permesso).
- `cd`, `mkdir`, `rmdir`: navigazione.

### enum4linux

Uno degli strumenti più completi per l’enumerazione SMB. Basato su `smbclient`, `rpcclient`, `nmblookup`.

- `enum4linux -a <target>` → esegue tutti i test disponibili.

Funziona bene soprattutto su server con null session abilitata. Può restituire:

- Lista degli utenti locali e del dominio.
- Lista dei gruppi.
- Informazioni su password policy.
- Share disponibili.

### rpcclient

Permette connessioni dirette a servizi RPC via SMB.

- `rpcclient -U "" <IP>` → login null.
- `enumdomusers`, `enumdomgroups`, `queryuser <RID>` → comandi per elencare e interrogare entità del dominio.

## Null Session

Una null session è una connessione SMB non autenticata. Alcuni sistemi mal configurati permettono a chiunque di connettersi e leggere dati sensibili.

Verifica:

- `rpcclient -U "" <target>`
- `smbclient -L //<target>/ -N`

Se funzionano, si è di fronte a una sessione anonima (null session), da cui estrarre:

- Nomi utenti
- Share
- Informazioni sul dominio

## Versioni vulnerabili e exploit

Alcuni server Windows esposti espongono versioni vulnerabili del protocollo SMB. Alcuni esempi:

- **SMBv1**: obsoleto e vulnerabile a EternalBlue (MS17-010)
- **Windows XP/2003/7 non patchati**: vulnerabili a vari buffer overflow
- **vsrvsvc**: vulnerabilità nei servizi RPC su SMB

### Verifica con Metasploit

- `use auxiliary/scanner/smb/smb_version`
- `use auxiliary/scanner/smb/smb_enumshares`
- `use auxiliary/scanner/smb/smb_login`

Per EternalBlue:

- `use exploit/windows/smb/ms17_010_eternalblue`

## crackmapexec

Strumento molto potente per testare credenziali su SMB (simile a un "Swiss army knife"):

- `crackmapexec smb <target> -u <user> -p <password>` → verifica accesso
- `crackmapexec smb <target> --shares` → elenca le condivisioni
- `crackmapexec smb <target> --users` → enumera utenti se possibile

Utile per testare accesso a grandi insiemi di target in modo automatizzato.

## Note operative

- Verifica sempre se l’accesso anonimo è possibile prima di usare credenziali.
- Tenta il download di file `.txt`, `.conf`, `.xml`, `.ini`, `.bak` per trovare password o configurazioni sensibili.
- Controlla se puoi caricare file in share accessibili da servizi web o eseguiti da processi di sistema.

L’enumerazione SMB può fornire una mappa dettagliata dell’ambiente Windows target e spesso consente escalation o lateral movement anche senza exploit.
