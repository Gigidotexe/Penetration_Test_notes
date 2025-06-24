<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
# SMB & Samba Enumeration & Exploitation

**SMB (Server Message Block)** è un protocollo di rete utilizzato principalmente nei sistemi Windows per la condivisione di file, stampanti e altre risorse. <br>
**Samba** è la sua implementazione libera in ambiente Linux/Unix, consentendo l’interoperabilità tra Windows e Linux. Entrambi operano sulle porte `445/TCP` (moderno, SMB Direct) e `139/TCP` (legacy, via NetBIOS). <br>

Un tempo SMB e NetBIOS erano strettamente legati: SMB sfruttava il protocollo NetBIOS per funzionare. Oggi, le reti moderne utilizzano SMB su porta 445 bypassando NetBIOS, che tuttavia può essere ancora rilevante in ambienti legacy o non aggiornati. 

---

## NetBIOS – Architettura e Rilevanza

**NetBIOS (Network Basic Input/Output System)** è un’API e set di protocolli utilizzato per consentire la comunicazione tra dispositivi in una rete locale. Offre tre principali servizi:

- **Name Service (NetBIOS-NS)** – usa la porta `137` per risolvere nomi host su reti locali (in passato utilizzato al posto del DNS);
- **Datagram Service (NetBIOS-DGS)** – usa la porta `138`, fornisce comunicazione connectionless e broadcasting per servizi tipo messaggistica;
- **Session Service (NetBIOS-SSN)** – opera sulla porta `139`, usato per comunicazioni orientate alla connessione.

Quando effettuiamo una scansione Nmap sulla porta 139, spesso il servizio restituito è proprio `netbios-ssn`, indicando l’attività di NetBIOS Session Service.

Per analizzare NetBIOS possiamo usare `nbtscan`, uno scanner leggero per identificare host che pubblicano nomi NetBIOS:

- `nbtscan <IP>` ⟶ scansione base per identificare nomi NetBIOS.
- `nbtscan -r <subnet>` ⟶ scansione ricorsiva di una subnet.
- `nbtscan -v <IP>` ⟶ output dettagliato con informazioni aggiuntive.

---
