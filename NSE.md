
# NSE
**Nmap Script Engine** é una feature molto utilizzata su Nmap che consente agli utenti di scrivere dei codici per autoimatizzare delle operazioni nel netwoek. <br>
Esistono vari tipi di script nmap:

- Network Discovery
- Versione Detection avanzati
- Vulnerability detection
- Backdoor Detection
- Bulnerability exploitation

Possiamo visualizzare questi script andando al path `/usr/share/nmap/scripts` e generalmente, quando si effettua una scansione nmap e si vogliono ottenere piú informazioni su un determinato servizio o protocollo, si ricercano gli script adatti da applicare alla nostra successiva scansione nmap e per poterlo fare in modo piú rapido, possiamo visualizzare solo gli elementi che ci interessano usando `grep`. 
se volessimo cercare tutti gli script riguardanti il protocollo `http` possimamo usare il comando:
```bash
ls -la /usr/share/nmap/scripts/ | grep http
```
cosí da filtrare i risultati solo al protocollo richiesto. <br>

Possiamo anche cercare degli script che rilevano delle vulnerabilitá: 
```bash
ls -la /usr/share/nmap/scripts/ | grep vulns
```
in modo da mostrare tutte le vulnerabilitá notee che é possibile identificare su un target tramite una scansione nmap. <br>

Qualche nse potrebbe richiedere degli argomenti e per poterli isnerire, usiamo il comando
```bash
--script-args "argomento1, argomento2"
```
possiamo visualizzare gli argomenti da specificare visualizzando direttamente lo script nmap con il comando `cat`. 

