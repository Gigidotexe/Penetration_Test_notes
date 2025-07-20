<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
# HTTP

Il protocollo **HTTP (HyperText Transfer Protocol)** è lo standard su cui si basa il web. Durante un penetration test, l’enumerazione HTTP è fondamentale per scoprire:

- Quali tecnologie e framework web sono in uso.
- Quali directory o file nascosti sono accessibili.
- Quali vulnerabilità note affliggono i componenti web.
- La presenza di pagine di login, aree admin o portali di debug.
- Il comportamento del server in risposta a richieste anomale.

Le porte più comuni per servizi web sono `80` (HTTP) e `443` (HTTPS), ma molti siti girano anche su `8080`, `8443`, `8000` e altre porte non standard.

<br>

## HTTP Methods
I metodi HTTP definiscono l’azione che il client vuole eseguire sulla risorsa identificata dall’URL. Ecco i principali: <br>
**GET**      | Recupera semplicemente dati dal server e non provoca modifiche allo stato delle risorse ed è idempotente <br>
**POST**     | Invia dati al server per creare o elaborare una nuova risorsa. <br>
**PUT**      | Carica una rappresentazione completa della risorsa all’URL specificato. Se esiste la aggiorna, altrimenti la crea. <br>
**DELETE**   | Elimina la risorsa indicata dall’URL. <br>
**PATCH**    | Applica modifiche parziali a una risorsa esistente, inviando solo gli elementi da aggiornare. <br>
**HEAD**     | Simile a GET, ma restituisce solo gli header di risposta senza il body. Utile per verificare l’esistenza di una risorsa o controllare metadata. <br>
**OPTIONS**  | Richiede al server quali metodi e header sono supportati per una determinata risorsa. <br>

<br>

## HTTP Requests and Response
Ogni volta che inseriamo un URL nella barra degli indirizzi o clicchiamo un link, il client invia un’**HTTP Request** una volta processata la richiesta dal server, verrà generata una **HTTP Response**. <br>

### HTTP Reqeust Components
La prima riga di una richiesta HTTP viene chiamata **request line** che definisce le informazioni essenziali per comunicare con il server. <br>
Si compone di tre elementi: <br>
**Metodo HTTP** (ad es. GET, POST, PUT, DELETE): specifica l’azione che il client vuole eseguire sulla risorsa. <br>
**URL** (Uniform Resource Locator): indica l’indirizzo della risorsa a cui si desidera accedere. <br>
**Versione HTTP** (ad es. HTTP/1.1, HTTP/2): determina la sintassi e le funzionalità del protocollo utilizzate per lo scambio. <br>

Subito dopo la request line, la richiesta HTTP può includere uno o più request headers, ciascuno su una nuova riga, per fornire al server informazioni aggiuntive utili al corretto trattamento della richiesta: <br>
**User‑Agent**: identifica il client che effettua la richiesta (browser, versione, sistema operativo). <br>
**Host**: specifica il nome del dominio o l’indirizzo IP del server di destinazione, indispensabile in ambienti con hosting virtuale. <br>
**Accept**: indica i tipi di media che il client è in grado di processare (ad es. text/html, application/json), permettendo al server di restituire il formato più appropriato. <br>
**Authorization**: trasporta le credenziali (token, username/password codificati) necessarie per accedere a risorse protette. <br>
**Cookie**: invia al server i cookie precedentemente impostati, utili per mantenere sessioni, preferenze utente o stato di autenticazione. <br>

Alcune richieste HTTP (in particolare con metodi come POST o PUT) possono includere un request body: il payload che il client invia al server. Questo corpo dati è spesso formattato in JSON. <br>
Il request body è opzionale e dipende dal metodo: le richieste GET o DELETE di solito ne sono prive, mentre le operazioni di creazione o modifica di risorse (POST, PUT, PATCH) lo utilizzano per trasportare i dati necessari al server per elaborare l’azione richiesta. <br>
```http
Esempio di HTTP Request
POST /api/resource HTTP/1.1
Host: example.com
User-Agent: MyApp/1.0
Accept: application/json
Authorization: Bearer <token_di_accesso>
Content-Type: application/json
Content-Length: 85
Connection: keep-alive

{
  "field1": "valore1",
  "field2": 123,
  "field3": true
}
```

### HTTP Response Components
I **response headers** forniscono informazioni aggiuntive utili al client per interpretare correttamente la risposta del server. <br>
I Response Headers contengono: <br>
**Content‑Type**: indica il tipo di media del body restituito (es. text/html, application/json), così che il client sappia come elaborarlo. <br>
**Content‑Length**: specifica la lunghezza, in byte, del corpo della risposta, permettendo al client di sapere quando la risposta è completa. <br>
**Set‑Cookie**: istruisce il client a salvare uno o più cookie, utili per gestire sessioni, preferenze utente o autenticazione nei successivi scambi. <br>
**Cache‑Control**: definisce direttive di caching (es. no‑cache, max‑age=3600), indicando al client o ai proxy per quanto tempo è consentito conservare la risorsa senza richiederla di nuovo. <br>

 il **response body** è la sezione opzionale che contiene il contenuto vero e proprio restituito dal server. A seconda della risorsa richiesta, il body può includere: <br>
**HTML**: il markup di una pagina web (tag, script, stili). <br>
**JSON** o **XML**: dati strutturati consumati da applicazioni o API. <br>
**File binari**: immagini, video, PDF o altri asset multimediali. <br>

Il formato del body deve corrispondere al valore dell’header Content‑Type, e la sua dimensione viene indicata da Content‑Length. Alcuni codici di stato (es. 204 No Content, 304 Not Modified) non prevedono un body, mentre per altri (es. 200 OK, 201 Created) è indispensabile per trasportare i dati richiesti. <br>
```http
Esempio di HTTP Response
HTTP/1.1 200 OK
Date: Mon, 21 Jul 2025 14:32:10 GMT
Server: Apache/2.4.46 (Unix)
Content-Type: application/json; charset=UTF-8
Content-Length:  ninety
Cache-Control: no-cache, no-store, must-revalidate
Set-Cookie: sessionId=abc123xyz; Secure; HttpOnly
Connection: keep-alive

{
  "status": "success",
  "data": {
    "id": 42,
    "name": "esempio",
    "active": true
  }
}
```

<br>

I **codici di stato HTTP** sono risposte standardizzate che il server invia al client per segnalare l’esito della richiesta. Ecco i più comuni: <br>

**200 OK** <br>
Indica che la richiesta è andata a buon fine e il server ha restituito i dati richiesti. È il “segnale verde” per qualsiasi operazione di lettura (GET) o successo di un’azione (POST, PUT). <br>

**301 Moved Permanently** <br>
La risorsa richiesta è stata spostata in modo permanente a un nuovo URL. Il client (o il motore di ricerca) deve aggiornare il link e puntare d’ora in avanti alla nuova posizione. <br>

**302 Found** <br>
Simile al 301, ma temporaneo: la risorsa si trova altrove per il momento, ma in futuro tornerà all’URL originale. Spesso usato per redirect di sessione o manutenzioni brevi. <br>

**400 Bad Request** <br>
La richiesta non è stata compresa dal server a causa di sintassi errata (es. JSON malformato, parametri mancanti). Bisogna correggere il payload o l’URL prima di riprovare. <br>

**401 Unauthorized** <br>
Non sono state fornite credenziali valide o il token di accesso non è riconosciuto: il client deve autenticarsi (header Authorization valido) per ottenere l’autorizzazione. <br>

**403 Forbidden** <br>
Il server ha compreso la richiesta, ma si rifiuta di esaudirla: l’utente non ha i permessi necessari, anche se autenticato correttamente. <br>

**404 Not Found** <br>
La risorsa richiesta non esiste sul server (URL inesistente o risorsa rimossa). È il codice più comune per link rotti o percorsi errati. <br>

**500 Internal Server Error** <br>
Errore generico del server durante l’elaborazione: indica problemi interni (bug, malfunzionamenti, eccezioni imprevisti). Il client non può risolverli, serve un intervento lato server. <br>

<br>

Le direttive di **Cache‑Control** definiscono il comportamento di caching sia sul client sia sui proxy intermedi, ottimizzando le performance e garantendo la freschezza dei contenuti:
**public** <br>
Indica che la risposta può essere memorizzata in cache da qualsiasi intermediario (proxy, CDN) e condivisa tra più utenti. Utile per risorse pubbliche e statiche (CSS, immagini). <br>
**private** <br>
Specifica che la risposta è destinata a un singolo utente e non deve essere salvata da cache condivise; solo il browser del client può conservarla. Ideale per pagine personalizzate o dati sensibili. <br>
**no‑cache** <br>
Non vieta il caching, ma impone al client di rinnovare la validità della risorsa contattando il server prima di riutilizzarla. Garantisce che non si usino contenuti obsoleti senza una verifica. <br>
**no‑store** <br>
Proibisce completamente il salvataggio della risposta in qualsiasi forma di cache; il client e i proxy non devono conservare neanche temporaneamente i dati. Fondamentale per informazioni altamente sensibili (dati bancari, token di autenticazione). <br>
**max‑age=<SECONDS>** <br>
Definisce per quanti secondi la risposta può restare in cache prima di dover essere riconsultata al server. Ad esempio, max‑age=3600 permette l’uso della risorsa memorizzata per un’ora, riducendo le richieste ripetute. <br>
<br>
Impostando le giuste direttive di Cache‑Control in risposta alle diverse esigenze (staticità vs. dinamismo, dati pubblici vs. riservati), si trova il giusto equilibrio tra efficienza di rete, carico sul server e sicurezza delle informazioni.


