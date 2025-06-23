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
> Serve a identificare utenti validi. L’output può essere salvato in un file e usato nel modulo `http_login` per ottimizzare il brute force.

---

## Webshell e Upload di File

Se è presente una funzione di upload:
- Verifica se è possibile caricare file `.php`, `.jsp`, `.asp`, `.exe`.
- Verifica se i file caricati sono accessibili pubblicamente.
- Controlla la validazione su estensione, MIME e contenuto.

---

## Tecniche Manuali

### View Source

Analizza il codice HTML:
- Commenti nascosti (`<!-- -->`)
- Path o endpoint JS (`fetch("/admin/api")`)
- Form nascosti o parametri (`action="/hidden"`)

### DevTools

Analizza:
- Header HTTP
- Cookie (`HttpOnly`, `Secure`, `SameSite`)
- Richieste AJAX

---

## Nikto

Scanner automatico per vulnerabilità:
```bash
nikto -h <URL>
```
Cerca file deprecati, directory comuni, problemi noti.

---

## Nmap NSE Script

```bash
nmap --script http-title <IP>         # titolo della pagina
nmap --script http-enum <IP>          # directory comuni
nmap --script http-methods <IP>       # metodi HTTP supportati
nmap --script http-headers <IP>       # header HTTP
```

---

## Considerazioni Finali

L’enumerazione HTTP può rivelare informazioni cruciali, tra cui:

- Path a script interni
- API non documentate
- Credenziali nei commenti
- File `.bak`, `.old`, `.zip` scaricabili

Le applicazioni web sono uno dei vettori più comuni per l’accesso iniziale in un attacco. Una buona fase di enumerazione può portare all’escalation dei privilegi o all’accesso completo.
