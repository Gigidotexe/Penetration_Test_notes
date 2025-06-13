L’enumerazione nei penetration test è una fase fondamentale che segue la ricognizione iniziale. Consiste nel raccogliere informazioni dettagliate sulle risorse esposte da un sistema target, come utenti, nomi di dominio, servizi attivi, condivisioni, configurazioni e vulnerabilità. L’obiettivo è ottenere una mappa completa dell’ambiente per identificare potenziali vettori di attacco.

Uno dei tool più utilizzati per questa fase è Metasploit, una piattaforma modulare per lo sfruttamento delle vulnerabilità. Serve a testare la sicurezza di un sistema simulando attacchi reali. Permette di gestire exploit, payload, scanner, e moduli ausiliari in modo molto strutturato. Si avvia scrivendo sul terminale il coamando `msfconsole` e, per poter creare dei worksapce é necessario che il database PostgreSQL sia attivo. Se non lo è, si può avviare con `service postgresql start`, perché è tramite questo database che Metasploit tiene traccia degli host, delle vulnerabilità trovate e delle sessioni attive.
È anche possibile caricare un file XML con  le scansioni effettuate con nmap dentro Metasploit per poter avere un ambiente pronto e dedicato alle vulnerabilità del target usando il comando `db_import <pathdelfiledaimportare>` e successivamente si usa `hosts` per avere una panoramica delle informazioni inserite e il comando `services` per visualizzare tutti i servizi inseriti. 
Dopo aver effettuato degli exploit, è possibile visualizzare anche i dati raccolti, con il comando `loot`, e le credenziali ottenute, con il commando `creds` dentro msfconsole. 

È anche possibile effettuare scansioni direttamente all’interno di Metasploit tramite il comando `db_nmap`, utile se si vuole mantenere tutto centralizzato nel workspace corrente.

Metasploit mette a disposizione dei moduli ausiliari che non eseguono exploit, ma svolgono compiti di supporto come scansioni, brute force, e raccolta di informazioni. Ne esistono centinaia, organizzati per categoria, e sono fondamentali nella fase di information gathering. Per esempio, dopo aver stabilito una sessione meterpreter, è possibile estendere la rete accessibile al framework usando 
`run autoroute -s <IP>`, che definisce una nuova rotta interna così da raggiungere host dietro la macchina compromessa e verificata con `route print`.

Una sessione meterpreter può essere messa in background semplicemente scrivendo `background`, e si può poi visualizzarla con `sessions`. Per riprenderla, si usa sessions `-i <numeroSessione>`.

