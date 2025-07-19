
# Introduzione al Social Engineering
L'ingegneria sociale é una tecnica utilizzata per manipolare individui con il fine di ottenere l'accesso non autorizzato a informazioni sensibili sfruttando la psicologia umana e la fiducia. <br>
A differenza degli attacchi tecnici, qui l’“exploit” è la fiducia o la curiosità della vittima: attraverso phishing via email, telefonate di pretexting, o persino incontri di persona (tailgating). <br>
In fase di ricognizione, raccoglie dati pubblici e personali (es. nome del responsabile IT, struttura organizzativa), utili a rendere l’attacco più credibile; nella fase successiva invia email mirate (spear phishing) proponendo link o allegati malevoli. <br> 
Se la vittima “cade”, l’assessment prosegue con l’escalation di privilegi, dimostrando come un semplice click possa compromettere reti interne. Questo tipo di test è fondamentale perché evidenzia la vulnerabilità più insidiosa: l’errore umano. <br>

Per integrare efficacemente l’ingegneria sociale all’interno di un penetration test, è innanzitutto cruciale definire con chiarezza gli obiettivi e i limiti dell’operazione: ogni scenario (phishing, vishing, baiting, tailgating ecc.) va pianificato in accordo con il committente per garantire che non vengano oltrepassate barriere legali o etiche. <br>
Durante la fase di **preparazione**, si selezionano i vettori di attacco più criticizzati dall’analisi del rischio (ad esempio, dirigenti di vertice per lo spear phishing, team help‑desk per la vishing, aree di accesso pubblico per il tailgating) e si definiscono i ruoli e le responsabilità all’interno del team di test. <br>

Successivamente, nella fase di **esecuzione**, è essenziale applicare un approccio multicanale e coordinato: per esempio, si può inviare un’email di spear phishing mirato e, dopo aver valutato l’efficacia dei click sui link, a valle sottoporre lo stesso gruppo a una chiamata di vishing che richiami la falsa campagna email, verificando la coerenza delle risposte. <br>
Parallelamente, un operatore “in incognito” può tentare il tailgating presso la sede aziendale, mentre un altro invia uno smishing con link a un presunto portale di aggiornamento software.

Per misurare i **risultati** e l’efficacia delle contromisure, si utilizzano metriche come il tasso di click sui link di phishing, il numero di credenziali rivelate su IVR o SMS, la percentuale di successo nei tentativi di pretexting e l’incidenza di accessi fisici non autorizzati. <br>
Questi dati vengono quindi organizzati in un report strutturato per aree di miglioramento:

Awareness e formazione – adozione di moduli e-learning interattivi, test di phishing bi‑mensili e workshop di role‑play per gestire richieste telefoniche sospette <br>
Procedure e policy – introduzione di checklist di autenticazione per reset password o interventi tecnici, definizione di protocolli di sorveglianza e badge anti‑tailgating <br>
Tecnologie di supporto – implementazione di soluzioni anti‑phishing a livello di gateway email, filtraggio SMS e blocco delle chiamate spoofing, utilizzo di sensori di prossimità per porte protette <br>

Infine, un penetration test di ingegneria sociale ben condotto non si conclude con la sola individuazione delle vulnerabilità, ma prevede un piano di remediation condiviso con il cliente, comprensivo di un programma di ricontrolli (re‑test) e di metriche di progresso che permettano di quantificare, nel tempo, l’innalzamento del livello di resilienza umana e organizzativa. Solo così l’errore umano – vero anello debole della catena di sicurezza – potrà trasformarsi in un punto di forza.
