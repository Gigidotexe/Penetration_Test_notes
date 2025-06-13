L’enumerazione FTP, questa è una tecnica usata per ottenere informazioni su un servizio FTP attivo, come la versione, l’accesso anonimo, le directory accessibili, i banner e l’elenco degli utenti. 
È applicabile su server che espongono il servizio FTP (**porta 21**). 
Spesso si comincia con moduli come `auxiliary/scanner/ftp/ftp_version`, che identificano la versione del servizio e permettono di capire se ci sono vulnerabilità note. È bene ricordare che l'uso di brute force potrebbe causare la disattivazione temporanea del servizio FTP come misura di sicurezza.

Per migliorare l’efficacia delle ricerche su Metasploit, si possono usare filtri come 
`search type:auxiliary name:ftp`, per restringere il campo ai soli moduli di tipo auxiliary legati al protocollo FTP.

Per collegarsi manualmente ad FTP basta usare il comando `ftp <IP> <port>` e una volta ottenuto l'accesso é possibile scaricare un file usando il comando `get<fileName>` 

