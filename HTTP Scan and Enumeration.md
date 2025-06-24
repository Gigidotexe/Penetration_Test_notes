<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
## Identificazione del Servizio
Il primo passo é quello di verificare che servizio é in esecuzione sulla macchina target usando `nmap`.
```bash
nmap -sV -p80,443 <IP>
```
Per poter analizzare piú a fondo il nostro target, usiamo degli script nse
```bash
nmap --script http-title <IP>        # titolo della pagina
nmap --script http-enum <IP>         # directory comuni
nmap --script http-methods <IP>      # metodi HTTP disponibili
nmap --script http-headers <IP>      # header di risposta
```

## Verifica dei metodi HTML
Una fase molto importante é quella di verificare quali metodi http accetta il target
```bash
davtest -url http://<IP>/directory     #sonda metodi (PUT, MKCOL, PROPFIND) e verifica upload se non é protetta da password
curl -X OPTIONS http://<IP>/directory -i
```
> Se durante la scansione dei metodi, risulta possibile caricare dei file sul server, possiamo tentare di caricare una webshell durante la fase di Exploiting
---

## Analisi manuale
Successivamente possiamo proseguire con l'analizzare manualmente il codice della pagina web
```bash
curl -I <target>          # mostra gli header HTTP (inclusi server, cookie, redirect)
curl -k https://<target>  # ignora errori di certificato SSL
```

L’header `Server` può rivelare il software in uso (es. `Apache`, `nginx`, `IIS`) e la versione, utile per correlare CVE. Alcuni server restituiscono anche l’header `X-Powered-By` (es. `PHP/5.6.40`, `ASP.NET`).
Analizzando 
- Commenti nascosti (`<!-- -->`)
- Path o endpoint JS (`fetch("/api/hidden")`)
- Campi nascosti, form sospetti, path non visibili
- File .cgi

> Estraendo gli header HTTP è possibile rilevare componenti lato server, comportamenti di redirect, tecnologie backend e potenziali vulnerabilità note associate alle versioni individuate.

> Se troviamo file .cgi usiamo l'nse <br>
> `nmap -sV <IP> --script=http-shellshock --script-args "http-shellshock.uri=/<script.cgi>"` <br>
> per scoprire se é vulnerabile a ShellShock

---

## Scansione con Metasploit
Anche metasploit ha dei moduli ausiliari per poter visualizzare la versione del server e l'header http
```bash
use auxiliary/scanner/http/http_version    # identifica la versione del server HTTP
use auxiliary/scanner/http/http_header     # estrae gli header HTTP
```
é consigliato anche verificare se un sistema é vulnerabile ad una vulnerabiltá nota usando vari moduli ausiliari come
```bash
auxiliary/scanner/http/webdav_scanner
exploit/windows/http/badblue_passthru
auxiliary/scanner/http/apache_mod_cgi_bash_env  #ShellShock
```
> se risulta vulnerabile vai alle sezioni apposite nel GitHub

## Scansione con Nikto

```bash
nikto -h <URL>
```

Nikto individua:
- File deprecati o nascosti
- Problemi di configurazione
- Versioni vulnerabili

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
