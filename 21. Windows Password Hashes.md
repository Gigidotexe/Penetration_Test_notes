# Windows Password Hashes

## Cos'è un hash di password?

In Windows, le password degli account utente **non vengono memorizzate in chiaro** ma sotto forma di **hash**, ossia stringhe generate tramite funzioni matematiche unidirezionali. Questo processo, noto come **hashing**, converte una password leggibile (es. `password123`) in una sequenza alfanumerica, irreversibile e costante.

## Dove sono memorizzati gli hash?

Gli hash vengono salvati all’interno del **SAM (Security Account Manager)**, un database locale presente in ogni sistema Windows. SAM si trova in:

`C:\\Windows\\System32\\config\\SAM`

Tuttavia, questo file è sempre **bloccato dal sistema operativo** poiché è gestito dal **kernel NT**. Per questo motivo, l’unico modo per accedere agli hash è **interagire con la memoria del processo LSASS (`lsass.exe`)**, dove vengono temporaneamente caricati durante il login.

### LSA (Local Security Authority)

LSA è il componente di Windows responsabile per:

- Verifica delle credenziali utente
- Creazione di access tokens
- Interazione con SAM

Gli attaccanti, per estrarre hash, devono accedere alla memoria di `lsass.exe`, solitamente tramite strumenti come **Mimikatz**, e con **privilegi SYSTEM**.

---

## Tipologie di Hash usati in Windows

### LM Hash (LAN Manager Hash)

Il **LM hash** è un metodo di hashing **obsoleto**, usato fino a Windows Server 2003 (incluso). Ecco le sue caratteristiche principali:

- **Non case sensitive**: `Password123` e `password123` generano lo stesso hash.
- La password viene convertita tutta in **maiuscolo**.
- Viene **spezzata in due blocchi da 7 caratteri**. Se la password è più corta di 14 caratteri, viene riempita con zeri.
- Ogni parte viene cifrata separatamente con **algoritmo DES**, ritenuto oggi insicuro.
- **Nessun salt**: significa che lo stesso input genera sempre lo stesso output.

**Esempio pratico:**

Una password come `password` viene trasformata in `PASSWOR` e `DXXXXXX`, poi ogni parte è cifrata con DES. Questo rende gli LM hash **altamente vulnerabili** a:

- **Brute-force**
- **Rainbow Tables**

---

### NTLM Hash (NT LAN Manager)

**NTLM** è il metodo moderno di hashing delle password su Windows (da Vista in poi). È **case sensitive**: distingue lettere maiuscole e minuscole. NTLM si basa sull’algoritmo di hashing **MD4**, applicato alla password in UTF-16LE.

**Caratteristiche principali:**

- Viene usato ancora oggi in molte reti aziendali.
- Non contiene un salt.
- Funziona bene per autenticazioni locali e di dominio.

**Esempio:**

La password `P@ssw0rd` genera un hash NTLM diverso rispetto a `p@ssw0rd`.

---

## NTLMv1 vs NTLMv2

### NTLMv1

- Utilizza hash MD4.
- Non utilizza HMAC né tecniche moderne.
- Più vulnerabile agli attacchi **Pass-the-Hash** e **Replay**.
- Utilizzato principalmente su vecchie reti.

### NTLMv2

- Introdotto per migliorare la sicurezza.
- Utilizza **HMAC-MD5**, include timestamp e informazioni di sessione.
- Più resistente agli attacchi replay.
- Richiede sincronizzazione dell'orologio tra client e server.

Molti sistemi moderni usano **solo NTLMv2** per sicurezza.

---

## Accesso al file SAM e privilegio richiesto

- Il file SAM è **criptato** e **inaccessibile** a utenti normali.
- Solo un utente con privilegi **SYSTEM** può accedere alla memoria di `lsass.exe` per estrarre gli hash.
- Il processo più comune è:
  - Ottenere una sessione privilegiata (es. via Meterpreter).
  - Migrare nel processo `lsass.exe`.
  - Caricare **Mimikatz** o **Kiwi**.
  - Eseguire `lsa_dump_sam` o `hashdump` per estrarre gli hash.

---

## Considerazioni Finali

La gestione degli hash su Windows è un punto critico per la sicurezza. La **presenza di NTLM** in sistemi moderni rappresenta ancora un rischio se non viene disabilitata o mitigata con altri sistemi di autenticazione (Kerberos, MFA).
