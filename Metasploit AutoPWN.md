# Metasploit AutoPWN
Esiste un plugin che permette di effettuare delle scansioni automatiche delle vulnerabilitá su sistemi analizzati con Metasploit Framework. <br>
Il Plugin é scaricabile su <a href="https://github.com/hahwul/metasploit-autopwn">GitHub</a> <br>
Dopo aver scaricato il file, va spostato dentro la directory `/usr/share/metasploit-framework/plugins` con il comando `mv` e successivamente importarla dentro msfconsole con il comando: <br>
`load db_autopwn` <br> 
per poter utilizzare questo plugin, basta scrivere il comando `db_autopwn` seguito dell'opzione che vogliamo eseguire. <br>
per visualizzare il messaggio di aiuto, usiamo `db_autopwn -h` 

se usiamo `db_autopwn =p -t` verrá effettuata una ricerca di tutti gli exploit (`-t`)per tutte le porte che abbiamo trovato aperte (`-p`)dopo aver effettuato una scansione dentro msfconsole. <br>
se volessimo limitare la scansione solo a determinate porte aperte, potremmo usare il comando `db_autopwn =p -t -PI <PORT>`

---
