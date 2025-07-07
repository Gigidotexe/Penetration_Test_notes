
# Searchsploit
L'intero database di **Explolit Database** é preinstalla in locale su kali linux, quindi possiamo accedervi in qualsiasi momento offline. <br>

Dobbiamo quindi aggiornare spesso il nostro sistema operativo kali linux per avere il database sempre aggiornato:
```bash
sudo apt-get update %% sudo apt-get update exploitdb -y
```
per poter visualizzare tutti gli exploit installati nel nostro Exploit Database locale, possiamo andare nella directory presente al path
```bash
ls -la /usr/share/exploitdb                # cartella di ExploitDB
ls -la /usr/share/exploitdb/exploits       # cartella di tutit gli exploits. Sono presenti altre cartelle in base alla categoria degli exploits
```
questo metodo di ricerca non é molto efficiente dato che mostra solo gli ID degli exploit e non i nomi dei singoli exploits. <br>

---

Esiste un comando su kali linux che ci permette di cercare meglio gli exploits per poi copiare il codice ed eseguirlo in seguito.
```bash
searchsploit                                      # per visualizzare la documentazione di questo comando
searchsploit -u                                   # effettua un upgrade degli exploit nel database locale dato che ExploitDB viene aggiornato giornalmente
searchsploit <nomeServizio+Versione>              # per poter cercare degli exploit nel database in base al servizio e alla sua versione
searchsploit -m <exploitID>                       # per copiare lo script dell'exploit trovato (sulla destra)
searchsploit -c <nome>                            # per specificare che vogliamo effettuare una ricerca CASE SENSITIVE es.OpenSSH
searchsploit -t <nome>                            # per cercare gli exploit che hanno quel <nome> nel loro titolo. 
searchsploit -e "<nome>"                          # per cercare corispondenze esatte a un servizio es.OpenSSH 7.2p2
```
possamo anche cercare specificatamente in base ai filtri di Exploit Database:
```bash
searchsploit <filtro1> <filtro2> <filtro3>
```
su `<filtro1>`
|   |   |   |   |  |
|---|---|---|---|---|
| dos | local | remote | webapps | hardware |


su `<filtro2>`:
|   |   |   |   |
|---|---|---|---|
| aix          | go        | multiple   | solaris      |
| alpha        | hardware  | netbsd_x86 | solaris_mips |
| android      | hp-ux     | netware    | solaris_sparc|
| arm          | immunix   | nodejs     | solaris_x86  |
| ashx         | ios       | novell     | superh_sh4   |
| asp          | irix      | openbsd    | system_z     |
| aspx         | java      | openbsd_x86| tru64        |
| atheos       | json      | osx        | typescript   |
| beos         | jsp       | osx_ppc    | ultrix       |
| bsd          | linux     | palm_os    | unix         |
| bsdi_x86     | linux_crisv32 | perl   | unixware     |
| bsd_ppc      | linux_mips| php        | vxworks      |
| bsd_x86      | linux_ppc | plan9      | watchos      |
| cfm          | linux_sparc| python    | windows      |
| cgi          | linux_x86 | python2    | windows_x86  |
| ezine        | linux_x86-64| python3  | windows_x86-64|
| freebsd      | lua       | qnx        | xml          |
| freebsd_x86  | macos     | ruby       |              |
| freebsd_x86-64| magazine | sco        |              |

su `<filtro3>` puoi mettere qualiasi protocollo. 

---

## Fixing Exploits
Dopoa ver copiato un exploit con `searchsploit -m <exploitID>` andiamo a visionare il codice dell'exploit con un editor di testo:
```bash
nano <exploitID>
#oppure
vim <exploitID>
```
generalmente, ogni exploit ha una sua descrizione scritta con commenti dentro al codice in modo da spiegare il suo funzionamento. <br>
capita spesso anche che degli exploit abbiano delle variabili preimpostate o da impostare in base al nostro target, quindi é fortemente consigliato visionare il codice ed effettuare le dovute modifiche dopo aver letto con cura la descrizione dell'exploit stesso.

se dovessimo aver bisogno di netcat, possiamo trovarlo al path `/usr/share/windows-resources/binaries/nc.exe` preinstallato su kali linux.
