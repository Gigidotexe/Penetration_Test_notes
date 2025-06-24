# FTP
**FTP (File Transfer Protocol)** è un protocollo di rete standard utilizzato per il trasferimento di file tra client e server.  
Opera in genere sulla porta TCP **21**, e in alcuni casi sulla **20** per il trasferimento dati.  
È uno dei protocolli più semplici e longevi, ma anche tra i più vulnerabili, soprattutto in configurazioni legacy o poco sicure.

FTP supporta diverse operazioni:
- Navigazione tra directory
- Caricamento (upload) di file
- Scaricamento (download) di file
- Visualizzazione di permessi e struttura

La sua principale debolezza è che si tratta di un protocollo **testuale e in chiaro**: tutte le credenziali, i comandi e i dati viaggiano senza crittografia, esponendo l’intero flusso a possibili intercettazioni (es. tramite Wireshark o tcpdump).  
Inoltre, è frequente trovare server FTP con configurazioni errate, accessi anonimi aperti, directory sensibili non protette o vulnerabilità legate a versioni obsolete del software.

---
