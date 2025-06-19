
# Dumping Linux Password Hashes

I sistemi Linux supportano un'architettura **multiutente**, permettendo a più utenti di accedere e operare contemporaneamente. Questa caratteristica, se da un lato è utile per l'amministrazione condivisa e i servizi multi-sessione, dall’altro può diventare un **punto debole** per la sicurezza. Ogni utente è infatti un possibile bersaglio di attacchi come il **credential dumping**.

Le informazioni degli utenti si trovano in due file fondamentali:

- `/etc/passwd` → accessibile da tutti, contiene **username** e **informazioni generali** sull’utente
- `/etc/shadow` → accessibile **solo a root**, contiene gli **hash delle password**

Un esempio di riga di `/etc/shadow`:
```
student:$6$SALT$longhash:18930:0:99999:7:::
```

La stringa dopo il secondo `$` indica l’algoritmo di hashing utilizzato:

- `$1` → MD5
- `$2` → Blowfish
- `$5` → SHA-256
- `$6` → SHA-512

---

## 2 – Accesso al file /etc/shadow

Per poter leggere `/etc/shadow` è **obbligatorio avere privilegi root**. Questo file è crittografato e non modificabile dagli utenti comuni. Quindi, è necessario prima ottenere una shell privilegiata attraverso un exploit o un'escalation di privilegi.

---

## 3 – Dumping degli hash

Se abbiamo ottenuto una shell privilegiata, possiamo semplicemente leggere il contenuto di `/etc/shadow`:

```bash
cat /etc/shadow
```

Successivamente, è possibile **estrarre gli hash** e salvarli su un file per ulteriori analisi o attacchi offline (es. brute-force o dictionary attack con `john` o `hashcat`).

Se l'accesso è stato ottenuto tramite **Metasploit**, e abbiamo una sessione attiva, possiamo:

```bash
sessions -u <id>
```

Per upgradare la sessione ad una Meterpreter shell, se necessario.

Una volta nella sessione Meterpreter privilegiata:

```bash
run post/linux/gather/hashdump
```

Questo modulo recupera e visualizza automaticamente il contenuto di `/etc/shadow`.

---

## 4 – Cracking degli hash

Gli hash possono essere esportati e decifrati usando tools di cracking come:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashfile
```

oppure con il modulo di metasploit
```
use auxiliary/analyze/crack_linux 
```

su cui dove dev'essere impostato l'algoritmo di hashing in base al codice che si trova tra $.
come ad esempio, se si tratta di un algoritmo `SHA-512`:
```
set SHA512 true
```
per poter eseguire automaticamente jon the ripper con determinati settaggi in base al algoritmo. 

---
