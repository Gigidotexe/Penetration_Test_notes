# Vulnerability Scanning con Nessus

**Nessus** è uno dei tool più utilizzati al mondo per il **vulnerability scanning**. Sviluppato da **Tenable**, è uno strumento proprietario ma offre una **versione gratuita limitata** (Nessus Essentials), adatta per ambienti di test, laboratorio o piccoli contesti di scansione.

Il suo scopo principale è **identificare vulnerabilità note** su sistemi target, come:

* CVE note legate a software non aggiornato.
* Servizi esposti con configurazioni deboli.
* Porte aperte e servizi attivi.
* Credenziali di default.
* Errori di configurazione noti.

I risultati possono poi essere **esportati e analizzati** dentro altri strumenti come **Metasploit Framework**, migliorando così la fase di exploitation con dati concreti.

---

## Installazione e Accesso

Per Kali Linux, è necessario scaricare la versione **Debian** di Nessus dal <a href="https://www.tenable.com/products/nessus">sito ufficiale</a>


La versione **Essentials** permette fino a **16 scansioni gratuite** per account.

Dopo il download:

```bash
cd ~/Downloads
chmod +x Nessus-<versione>.deb
sudo dpkg -i Nessus-<versione>.deb
```

Per avviare il servizio:

```bash
sudo systemctl start nessusd.service
```

Poi, accedi tramite browser all'indirizzo:

```
https://<IP_locale>:8834
```

(es. `https://127.0.0.1:8834`)

Da qui:

* Seleziona **Nessus Essentials**.
* Crea un nuovo account per usare l'interfaccia.
* Inserisci il codice di attivazione ricevuto via email.

---

## Creazione della Scansione

Dopo l'accesso:

* Seleziona **New Scan** > **Basic Network Scan**.
* Inserisci l'**IP della macchina target** oppure un intervallo (es. `192.168.1.0/24`).
* Nella sezione **Discovery**, seleziona `Port scan (common ports)` per velocità e precisione.
* Nella sezione **Assessment**, lascia le opzioni di default (rilevamento vulnerabilità, configurazioni, ecc.).

Avvia la scansione e attendi il completamento.

---

## Analisi dei Risultati

Una volta terminata la scansione, Nessus mostrerà:

* Servizi attivi
* Porte aperte
* Software rilevato
* **CVE** associate a vulnerabilità trovate
* Grado di criticità (basso, medio, alto, critico)

I risultati possono essere esportati in formato **.nessus** (XML), **.html** o **.pdf**.

---

## Integrazione con Metasploit

Per importare i risultati in **Metasploit Framework**:

```bash
db_import /path/to/report.nessus
```

Dopo l'importazione:

* Usa `vulns -p <porta>` per vedere le vulnerabilità specifiche.
* Analizza i risultati con `hosts`, `services`, `vulns` per visualizzare ciò che Nessus ha trovato.

Puoi cercare exploit con:

```bash
search cve:<CVE_code>
```

Esempio:

```bash
search cve:2021-3156
```

---

## Altre tecniche per l'identificazione delle vulnerabilità

Oltre alla scansione di rete standard, Nessus supporta:

### Credentialed Scan

Fornendo credenziali valide (SSH, SMB, RDP), Nessus può effettuare una **scansione autenticata** che rivela:

* Patch mancanti.
* Vulnerabilità non visibili dall'esterno.
* Configurazioni errate lato utente.

### Plugin Feed Aggiornato

Assicurati che il feed dei plugin di Nessus sia aggiornato. Questi contengono le firme e i controlli per vulnerabilità nuove.

### Custom Policy Scan

Permette di creare policy personalizzate per settori specifici (es. compliance, PCI-DSS, scansioni su cloud provider, Active Directory...)

---
