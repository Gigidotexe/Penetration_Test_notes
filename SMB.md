SMB (Server Message Block) è un protocollo di rete utilizzato per la condivisione di file, stampanti e altre risorse tra dispositivi in una rete, principalmente in ambienti Windows. Permette a un computer di accedere ai file su un altro come se fossero locali, gestendo operazioni come lettura, scrittura e autenticazione.
È comunemente usato nei contesti aziendali e può essere un vettore di attacco se mal configurato

Per poter verificarene la versione, é possibile usare il modulo `auxiliary/scanner/smb/smb_version` di Metasploit e successivamente é possibile tentare di enumerare tutti gli utenti usando il modulo `auxiliary/scanner/smb/smb_enumusers` ed enumerare le cartelle condivise con il modulo `auxiliary/scanner/smb/smb_enumshares`. 

Per poter reperire le credenziali é possibile usare il modulo `auxiliary/scanner/smb/smb_login` che, fornendo delle liste di usernames e passwords, é possibile tentare un attacco a dizionario. 

È importante impostare `STOP_ON_SUCCESS` su `false` per ottenere più credenziali possibili e non fermarsi al primo accesso riuscito.

Una volta ottenute le credenziali, si può usare il comando `smbclient -L <IP> -U <username>` per vedere tutte le share disponibili, e poi connettersi a una specifica con `smbclient //<IP>/<sharename> -U <username>`

Per ottenere la versione SMB via Nmap si può usare `--script=smb-os-discovery`, che fornisce anche il nome del computer NetBIOS. Per determinare il nome NetBIOS direttamente si può usare `nmblookup -A <IP>`, che interroga il servizio NetBIOS di un host per ottenere il nome della macchina e il gruppo di lavoro.

Esiste un comando su kali pensato per automatizzare la raccolta di informazioni da un target SMB ed é `enum4linux -a <IP>`
Funziona meglio su sistemi Windows vecchi o Samba male configurato.
Questo comando fa molto rumore nei file di log del target dato che manda tante richieste e quindi potrebbe essere facilmente bloccabile da un firewall. 

Con questo script bash é possibile visualizzare il contenuto delle shares:
```
#!/bin/bash
while read share; do
  echo "checking: $share"
  smbclient //<IP>/"$share" -N -c "ls" 2>&1 | tee -a smb_results.txt
  echo "-----------------------------------------"
done < shares.txt
```
questo script esegue, per ogni elemento presente all’interno del file share.txt nella stessa directory dello script, un ciclo con il comando `smbclient //<IP>/"$share" -N -c "ls" 2>&1` che esegue richieste smb al IP indicato aprendo la share del file ($share) in modalitá anonima (usando -N per non richiedere la password) e `-c “ls”` esegue il comando `ls` unendo l’output standard con il messaggio di errore. 

Dopo aver trovato un file che ci interessa, facciamo l’accesso a quella share con `smbclient //<IP>/<sharename> -N` e successivamente usiamo `get <file>` per scaricare nella directory corrente il file indicato

