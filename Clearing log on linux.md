
# Eliminare le tracce su Linux
Così come su windows, conviene inserire tutti i file che installiamo dentro un sistema linux dento la cartella `/tmp` dato che é una cartella poco frequentata e ogni volta che il sistema viene riavviato, questa cartella elimina il suo contenuto. <br>
Prestiamo anche attenzione al fatto che tutti i comandi che vengono eseguiti dentro una macchina linux, vengono salvati nella history e quindi é possibile identificare tutte le operazioni effettuate durante un Penetration Test. <br> 
```bash
cd ~/home
cat .bash_history                  # file dove vongono salvati i comandi eseguiti su linux
cat /dev/null > .bash_history      # eliminare il contenuto del file 
history                            # per vedere la cronologia dei comandi eseguiti
gistory -c                         # elimina la history

ls -la | grep .bashrc  
```
