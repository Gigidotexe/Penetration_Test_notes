# Alternate Data Streams (ADS)

## Cos'è un Alternate Data Stream?

Gli **Alternate Data Streams (ADS)** sono una caratteristica del file system **NTFS** (New Technology File System), utilizzato da Windows. In NTFS, ogni file può avere uno o più "stream" o flussi di dati, oltre al contenuto principale del file.

Questa funzionalità permette di associare più flussi di dati a un singolo file, il che può essere utilizzato legittimamente per contenere metadati o altre informazioni strutturali, ma anche abusato dagli attaccanti per **nascondere codice malevolo o file eseguibili**.

## I due flussi principali: Data Stream e Resource Stream

### 1. Data Stream (flusso principale)

È il contenuto visibile e modificabile del file. Quando apri un file `.txt` in Notepad, quello che leggi e modifichi è il **Data Stream**.

**Esempio**:
- Creiamo un file `note.txt` con il comando:
  `echo Ciao mondo > note.txt`
- Quando lo apriamo con Notepad, vediamo "Ciao mondo": è il contenuto del Data Stream.

### 2. Resource Stream (ADS)

È uno o più flussi di dati aggiuntivi nascosti che **non sono visibili direttamente all’utente**. Questi stream possono contenere altri file, dati, script o eseguibili.

**Esempio con file MP3**:
- Un file `musica.mp3` può contenere come Data Stream il brano musicale e come Resource Stream, ad esempio, la copertina dell’album o metadati ID3 (titolo, artista, ecc.).

## Utilizzo degli ADS per nascondere un payload

Questa tecnica è spesso impiegata dagli attaccanti per eludere antivirus e strumenti di rilevamento statico, sfruttando il fatto che i file ADS **non compaiono nei normali elenchi di directory** (`dir`) e non vengono eseguiti automaticamente.

### Esempio pratico con file `.txt`:

Apriamo il terminale di Windows (cmd) e digitiamo:

`notepad test.txt:secret.txt` ⟶ apre il Resource Stream "secret.txt" associato a "test.txt". <br>

Scrivendo dentro e salvando, abbiamo effettivamente creato un file nascosto `secret.txt` dentro `test.txt`.  
**Nota**: eseguendo `dir` nella directory, **`secret.txt` non apparirà mai**.  
Per rileggerlo, va riaperto con:

`notepad test.txt:secret.txt` ⟶ riapre il flusso ADS nascosto. <br>

### Nascondere un payload EXE dentro un file .txt:

Supponiamo di avere un payload chiamato `payload.exe`, possiamo nasconderlo così:

`type payload.exe > test.txt:winpeas.exe` ⟶ nasconde `payload.exe` come stream chiamato `winpeas.exe` dentro `test.txt`. <br>

Il file `test.txt` continua a sembrare un normale file `.txt`.

### Esecuzione del payload nascosto

Per eseguire il payload possiamo **creare un collegamento simbolico** usando `mklink`, ma il comando va corretto.  
Questa è la sintassi corretta:

`mklink payload.exe C:\\Temp\\test.txt:winpeas.exe` ⟶ crea un link simbolico eseguibile al file nascosto. <br>

Una volta creato il link `payload.exe`, possiamo semplicemente eseguire:

`payload.exe` ⟶ esegue lo stream `winpeas.exe` nascosto dentro `test.txt`. <br>

Questa tecnica consente di **mantenere l’eseguibile malevolo nascosto**, aggirando il rilevamento di antivirus superficiali che non analizzano gli ADS.

## Importanza per il pentester

ADS è un potente meccanismo per:

- Offuscamento di file
- Evasione AV
- Persistenza su sistemi compromessi

Tuttavia, è importante ricordare che molti EDR avanzati (e antivirus aggiornati) analizzano oggi anche gli ADS.
