# RSYNC
rsync è uno strumento potentissimo per la sincronizzazione e la copia efficiente di file e directory tra sistemi locali o remoti. <br>
È molto utilizzato sia in ambito sistemistico che DevOps, e trova applicazioni in backup, deploy, mirror di server e trasferimenti sicuri di grandi quantità di dati.

A differenza di `cp` o `scp` confronta i file tra sorgente e destinazione trasferendo solo le differenze. Questo significa che, se hai già trasferito un file in passato e lo rilanci, copierà solo ciò che è cambiato, rendendolo molto veloce e leggero.

rsync usa di default il protocollo rsync://, ma nella pratica si usa quasi sempre tramite SSH per motivi di sicurezza. 

---

## Quando é vulnerabile? 
Il servizio rsync diventa vulnerabile in presenza di configurazioni errate o troppo permissive, che possono esporre dati sensibili o persino permettere l'accesso in scrittura a utenti non autenticati. <br>
Il caso più comune é quando il server rsync espone moduli (directory) accessibili senza autenticazione, permettendo a chiunque di visualizzare l'elenco dei moduli (`rsync://IP/`), navigare il contenuto e scaricare tutti i file. <br>

