# Nmap Scanning

Nmap (Network Mapper) è uno degli strumenti fondamentali nella fase di information gathering attivo e viene utilizzato per l’enumerazione delle porte, l’identificazione dei servizi, la rilevazione del sistema operativo e la scoperta di vulnerabilità. È uno strumento estremamente versatile che supporta numerosi protocolli, tecniche evasive e script automatizzati.

## Tipi di scansione

- `-sS`: TCP SYN scan (half-open). È la modalità predefinita e una delle più silenziose.
- `-sT`: TCP connect scan. Usa la chiamata di sistema `connect()`, più rumorosa.
- `-sU`: UDP scan. Richiede tempo e produce molti falsi positivi.
- `-sN`, `-sF`, `-sX`: scan stealth alternativi (nessun flag, FIN, Xmas), usati per evadere IDS.

## Ping Scan e host discovery

Per identificare host attivi senza scansionare porte:

- `nmap -sn <target>`: esegue solo la fase di discovery.
- `-Pn`: disabilita il ping (utile se ICMP è filtrato).
- `-PS<port>`: invia pacchetti TCP SYN su porte specifiche.
- `-PA<port>`: usa pacchetti TCP ACK.
- `-PE`, `-PP`, `-PM`: ping ICMP Echo, Timestamp, e Netmask.

## Scansione delle porte

- `-p <port>`: specifica una o più porte.
- `-F`: esegue una scansione veloce sulle porte più comuni.
- `--top-ports <n>`: scansiona le `n` porte più usate.
- `-p-`: scansiona tutte le 65535 porte.

## Rilevamento versione e OS

- `-sV`: rileva la versione del servizio in esecuzione.
- `--version-intensity <0-9>`: controlla il livello di aggressività della rilevazione.
- `-O`: rilevamento del sistema operativo.
- `--osscan-guess`: tenta una stima anche in mancanza di certezza.

## NSE – Nmap Scripting Engine

Gli script NSE estendono le capacità di Nmap con test specifici. Si trovano in `/usr/share/nmap/scripts`.

Categorie principali:

- `auth`, `broadcast`, `brute`, `default`, `discovery`, `dos`, `exploit`, `external`, `fuzzer`, `intrusive`, `malware`, `safe`, `version`, `vuln`.

Esempi:

- `nmap --script=default <target>`
- `nmap --script=http-title <target>`
- `nmap --script=vuln <target>`
- `nmap --script-help=<nome_script>`: mostra la documentazione dello script.

## Tecniche di evasione

Per scansioni più stealthy si possono usare:

- `-f`: frammentazione dei pacchetti.
- `-D <decoy1,decoy2,...>`: uso di indirizzi decoy per mascherare l'origine.
- `-S <IP>`: spoofing dell’indirizzo IP sorgente (richiede privilegi).
- `--data-length <n>`: padding extra ai pacchetti.
- `-T<0-5>`: timing template, da `-T0` (più stealth) a `-T5` (più aggressivo).
- `--spoof-mac <vendor|random>`: cambia l’indirizzo MAC.

## Output

Per salvare i risultati delle scansioni:

- `-oN`: output normale.
- `-oX`: XML.
- `-oG`: grepable.
- `-oA <prefix>`: genera tutti e tre i formati con un solo comando.

## Scansione aggressiva

Il flag `-A` esegue:

- OS detection
- Version detection
- Script NSE di default
- Traceroute

Comando completo:

`nmap -A -T4 <target>`

Nmap è uno strumento potente ma va usato con criterio. Una buona scansione Nmap ben pianificata può fornire quasi tutte le informazioni necessarie per preparare un attacco efficace, soprattutto se combinata con i dati raccolti durante l’analisi passiva e l’enumerazione DNS.
