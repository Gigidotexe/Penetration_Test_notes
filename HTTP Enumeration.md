# HTTP Enumeration

Il protocollo **HTTP (HyperText Transfer Protocol)** è lo standard su cui si basa il web. Durante un penetration test, l’enumerazione HTTP è fondamentale per scoprire:

- Quali tecnologie e framework web sono in uso.
- Quali directory o file nascosti sono accessibili.
- Quali vulnerabilità note affliggono i componenti web.
- La presenza di pagine di login, aree admin o portali di debug.
- Il comportamento del server in risposta a richieste anomale.

Le porte più comuni per servizi web sono `80` (HTTP) e `443` (HTTPS), ma molti siti girano anche su `8080`, `8443`, `8000` e altre porte non standard.

---

## Identificazione Iniziale

La prima fase consiste nel visitare manualmente l’host nel browser o tramite `curl`:
```bash
curl -I <target>          # mostra gli header HTTP (inclusi server, cookie, redirect)
curl -k https://<target>  # ignora errori di certificato SSL
```

L’header `Server` può rivelare il software in uso (es. `Apache`, `nginx`, `IIS`) e la versione, utile per correlare CVE. Alcuni server restituiscono anche l’header `X-Powered-By` (es. `PHP/5.6.40`, `ASP.NET`).

Con Metasploit, è possibile utilizzare moduli ausiliari:
```bash
use auxiliary/scanner/http/http_version    # identifica la versione del server HTTP
use auxiliary/scanner/http/http_header     # estrae gli header HTTP per ottenere informazioni su configurazione e tecnologia
```

> Estraendo gli header HTTP è possibile rilevare componenti lato server, comportamenti di redirect, tecnologie backend e potenziali vulnerabilità note associate alle versioni individuate.

---

## Fingerprinting con WhatWeb e Wappalyzer

Strumenti per identificare CMS, framework e tecnologie usate dal sito:
```bash
whatweb <URL>
```
`wappalyzer` è disponibile come estensione del browser e mostra librerie JS, linguaggi backend, analytics e altro.

---

## Enumerazione delle Directory

### Robots.txt

Spesso accessibile via:
```bash
http://<target>/robots.txt
```

Contiene percorsi che il creatore del sito non vuole siano indicizzati. Spesso questi path nascondono directory riservate:
```text
User-agent: *
Disallow: /admin/
Disallow: /backup/
```

Con Metasploit:
```bash
use auxiliary/scanner/http/http_robots_txt
```

Dopo aver identificato una directory:
```bash
curl http://<IP>/<directory>
```

### Directory e File Brute Forcing

#### Metasploit:
```bash
use auxiliary/scanner/http/dir_scanner      # directory brute force
use auxiliary/scanner/http/files_dir        # file brute force, definibile per estensione
```

#### Gobuster
```bash
gobuster dir -u http://<target> -w /usr/share/wordlists/dirb/common.txt -t 50 -x php,txt,bak
```

#### Dirb
```bash
dirb http://<target> /usr/share/wordlists/dirb/common.txt
```

#### Ffuf
```bash
ffuf -u http://<target>/FUZZ -w <wordlist>
```

---

## Enumerazione di CMS e Pannelli Noti

Identificato un CMS, si possono usare tool specifici:

- `wpscan --url <URL>` → WordPress
- `droopescan scan drupal -u <URL>` → Drupal
- `joomscan` → Joomla

Path noti da controllare:
- `/admin`, `/login`, `/wp-login.php`, `/phpmyadmin`

---

## Pagine Protette e Login Brute Force

Con Metasploit:
```bash
use auxiliary/scanner/http/http_login
set AUTH_URI /pagina_riservata
unset USERPASS_FILE
```
> `unset USERPASS_FILE` è consigliato se si desidera specificare separatamente file di username e password, oppure testare singole credenziali.

Se il server è Apache:
```bash
use auxiliary/scanner/http/apache_userdir_enum
```
> Questo modulo cerca directory `~username` valide. L’output può essere salvato in un file e usato nel modulo `http_login` per ottimizzare il brute force.

---

## Webshell e Upload di File

Se è presente una funzione di upload:
- Verifica se è possibile caricare file `.php`, `.jsp`, `.asp`, `.exe`.
- Verifica se i file caricati sono accessibili pubblicamente.
- Controlla la validazione su estensione, MIME e contenuto.

Un upload vulnerabile può portare a **Remote Code Execution** o shell inversa.

---

## Tecniche Manuali

### View Source

Analizza il codice HTML:
- Commenti nascosti (`<!-- -->`)
- Path o endpoint JS (`fetch("/api/hidden")`)
- Campi nascosti, form sospetti, path non visibili

### DevTools

Analizza:
- Header HTTP
- Cookie: `Secure`, `HttpOnly`, `SameSite`
- Parametri GET/POST nelle chiamate AJAX

---

## Scansione con Nikto

```bash
nikto -h <URL>
```

Nikto individua:
- File deprecati o nascosti
- Problemi di configurazione
- Versioni vulnerabili

---

## Nmap – HTTP NSE Script

```bash
nmap --script http-title <IP>        # titolo della pagina
nmap --script http-enum <IP>         # directory comuni
nmap --script http-methods <IP>      # metodi HTTP disponibili
nmap --script http-headers <IP>      # header di risposta
```

---

## Vulnerability Scanning con WMAP (Metasploit)

**WMAP** è un modulo di Metasploit dedicato alla scansione di vulnerabilità web. Va caricato manualmente:

```bash
load wmap
```

Per aggiungere un target:
```bash
wmap_sites -a <IP>             # aggiunge il sito alla lista
wmap_targets -l                # mostra i target aggiunti
wmap_targets -h                # spiega tutte le opzioni disponibili
```

Per definire un target specifico:
```bash
wmap_targets -t http://<IP>
```

Per avviare la scansione:
```bash
wmap_run
```

> La scansione verrà eseguita in ordine partendo dal primo target in lista. Una volta terminate le analisi, WMAP mostrerà anche quali moduli di Metasploit possono essere usati per effettuare attacchi exploit sugli elementi vulnerabili individuati.

Esempio:
> Se WMAP identifica una directory vulnerabile all'upload arbitrario, potresti sfruttarla con il modulo:  
> `exploit/unix/webapp/php_upload_exec`

---
