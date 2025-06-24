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
use auxiliary/scanner/http/http_header     # estrae gli header HTTP
```

> Estraendo gli header HTTP è possibile rilevare componenti lato server, comportamenti di redirect, tecnologie backend e potenziali vulnerabilità note associate alle versioni individuate.

---

## Nmap
Esistono degli NSE per poter analizzare il sito web target:
```bash
nmap --script http-title <IP>        # titolo della pagina
nmap --script http-enum <IP>         # directory comuni
nmap --script http-methods <IP>      # metodi HTTP disponibili
nmap --script http-headers <IP>      # header di risposta
```

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

## Directory e File Brute Forcing

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

## Robots.txt
Dentro i siti web esiste un file di testo chiamato `robots.txt` che serve per dare istruzioni ai motori di ricerca su quali directory possono essere visitate e indicizzate e quali devono essere evitate. <br>
Non protegge i contenuti, ma aiuta a controllare la visibilità del sito nei risultati di ricerca
```bash
http://<target>/robots.txt
```

Spesso il file `robots.txt` nasconde directory riservate:
```text
User-agent: *
Disallow: /admin/
Disallow: /backup/
```

Dopo aver identificato una directory nascosta, possiamo vederne il contenuto con:
```bash
curl http://<IP>/<directory>
```
oppure interagendo direttamente dal browser.

Esiste un modulo Metasploit per automatizzare questo processo:
```bash
use auxiliary/scanner/http/http_robots_txt
```
questo modulo scarica e analizza il file `robots.txt` di un sito, cercando percorsi nascosti ma accessibili che possono rivelare informazioni utili o portare a vulnerabilità.


---

## Enumerazione di CMS e Pannelli Noti
Anallizzare i CMS (Content Management System) serve a identificare quale piattaforma viene utilizzata per gestire un sito web, come WordPress, Joomla, Drupal, Magento, ecc.<br>
Per analizzare i CMS, si usa `whatweb`:
```bash
whatweb <URL>
```
Dopo aver trovato dei CMS: 
- `wpscan --url <URL>` → WordPress
- `droopescan scan drupal -u <URL>` → Drupal
- `joomscan` → Joomla

Path noti da controllare:
- `/admin`, `/login`, `/wp-login.php`, `/phpmyadmin`

> `wappalyzer` è disponibile come estensione del browser e mostra librerie JS, linguaggi backend, analytics e altro.

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
