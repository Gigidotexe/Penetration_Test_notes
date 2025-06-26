# RSYNC
rsync è uno strumento potentissimo per la sincronizzazione e la copia efficiente di file e directory tra sistemi locali o remoti. <br>
È molto utilizzato sia in ambito sistemistico che DevOps, e trova applicazioni in backup, deploy, mirror di server e trasferimenti sicuri di grandi quantità di dati.

A differenza di `cp` o `scp` confronta i file tra sorgente e destinazione trasferendo solo le differenze. Questo significa che, se hai già trasferito un file in passato e lo rilanci, copierà solo ciò che è cambiato, rendendolo molto veloce e leggero.

rsync usa di default il protocollo rsync://, ma nella pratica si usa quasi sempre tramite SSH per motivi di sicurezza. 

---

## Quando é vulnerabile? 
Il servizio rsync diventa vulnerabile in presenza di configurazioni errate o troppo permissive, che possono esporre dati sensibili o persino permettere l'accesso in scrittura a utenti non autenticati. <br>
Il caso più comune é quando il server rsync espone moduli (directory) accessibili senza autenticazione, permettendo a chiunque di visualizzare l'elenco dei moduli (`rsync://IP/`), navigare il contenuto e scaricare tutti i file. <br>

---

## Identificazione del Servizio
Il primo passo é quello di verificare che servizio é in esecuzione sulla macchina target.
### Nmap
```bash
nmap -p 873 <IP>
```
successivamente usare qualche nse: 
```bash
nmap -p 873 --script=rsync-list-modules <IP>
nmap --script=rsync-brute -p 873 <IP>
```

### Metasploit
```bash
use auxiliary/scanner/rsync/modules_list
set RHOST <IP>
run
```

---

## Testare Accesso diretto
É probabile che RSYNC esponga a chiunque i moduli al suo interno. Per testare questo comportamento usiamo il comando
```bash
rsync rsync://<IP>/
```
se vengono visualizzati dei moduli, possiamo muoverci tra essi aggiungendo i vari nomi dei moduli che troviamo ad esempio, se `rsync rsync://<IP>/` dovesse restituire `folder1` e `folder2`, possiamo incrementare il path aggiungendo ad esempio `folder 1`
```bash
rsync rsync://<IP>/folder1
```
che a sua volta mostrerá altro contenuto. <br>
per poter estrarre qualche file usiamo
```bash
rsync -av rsync://<IP>/folder1 .
```
dove `-a` signica fare copie perfette, mantenendo ogni attributo possibile dei file e `-v` sta per verbose e quindi viene scritto in output quello che sta accadendo.
> mi raccomando ad inserire il `.` a fine comando che sta ad indicare che vogliamo scaricare il file nella nostra directory corrente locale, altrimenti avremmo inserito il path di dove avremmo voluto scaricare il file.
