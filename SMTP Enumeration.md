# SMTP Enumeration

SMTP (Simple Mail Transfer Protocol) è il protocollo usato per inviare email. È spesso attivo su server pubblici per consentire la comunicazione tra mail server. Le porte comuni sono:

- `25` (standard)
- `465` (SMTP con SSL)
- `587` (SMTP con TLS)

## Perché è utile nel penetration testing?

Anche se non pensi di voler "mandare email", un server SMTP può fornirti:

- Nomi validi di utenti o account
- Informazioni sul dominio e nome host
- Comportamenti del sistema rispetto a nomi inesistenti
- In alcuni casi, accesso a servizi interni (relay o spoofing)

## Fase 1 – Scansione iniziale

Verifica la presenza di un servizio SMTP:

`nmap -p 25,465,587 -sV <IP>`

Con script dedicati:

`nmap -p 25 --script smtp-commands,smtp-enum-users,smtp-open-relay <IP>`

Questi script cercano di:

- Capire i comandi supportati dal server (`EHLO`, `VRFY`, `EXPN`)
- Verificare se è aperto a relay (invio di email da chiunque)
- Enumerare utenti esistenti

## Fase 2 – Connessione manuale via telnet o netcat

Puoi collegarti a mano per dialogare con il server:

`elnet <IP> 25`

Eseguendo:

`HELO test.com` <br>
`VRFY administrator` <br>
`VRFY root` <br>
`EXPN users`

Significato:

- `HELO`: introduce la connessione
- `VRFY`: chiede se un utente esiste
- `EXPN`: espande una mailing list

Esempio di risposta:
```
250 User exists
550 User unknown
```

Con questi messaggi puoi capire se certi nomi utente sono validi. Questo è utile per attacchi di brute-force futuri.

## Fase 3 – User Enumeration automatizzata

### Metasploit

`use auxiliary/scanner/smtp/smtp_enum` <br>
`set RHOSTS <IP>` <br>
`set USER_FILE <path>` <br>
`run` <br>

### smtp-user-enum

Strumento molto veloce:

`smtp-user-enum -M VRFY -U users.txt -t <IP>`

Modalità supportate:

- `VRFY`
- `EXPN`
- `RCPT` (simula invio mail per vedere se accetta destinatario)

Attenzione: non tutti i server rispondono a questi comandi. I moderni SMTP spesso li disabilitano per evitare questa tecnica.

## Fase 4 – Relay aperto (open relay)

Un open relay consente di inviare email attraverso il server senza autenticazione. È un grosso problema di sicurezza: può essere usato per spam o phishing.

Verifica con:

`telnet <IP> 25` <br>
`MAIL FROM:tuo@email.com` <br>
`RCPT TO:vittima@altrodominio.com` <br>
`DATA` <br>
`<Testo dell’email>` <br>

Se l’email viene accettata, il server è un relay aperto. Questi casi sono rari ma molto gravi.

## Fase 5 – Raccolta informazioni

Durante l’handshake SMTP, il server potrebbe fornire:

- Nome host
- Versione software
- Nome dominio
- Estensioni supportate (`STARTTLS`, `AUTH`, `PIPELINING`)

Esempio di output:
```
220 mail.example.com ESMTP Postfix (Ubuntu)
250-mail.example.com
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-AUTH LOGIN PLAIN
```

Il banner ti dice già molto: dominio, tipo di server (`Postfix`, `Exim`, `Sendmail`), e funzionalità disponibili.

## Considerazioni finali

SMTP è un servizio spesso sottovalutato nel pentesting, ma può:

- Rivelare nomi utenti (da testare su SSH, RDP, ecc.)
- Mostrare informazioni di sistema
- Aprire la porta a spoofing o escalation

Se trovi un server SMTP attivo, **parlagli** come farebbe un client email. Spesso ti risponde più di quanto dovrebbe.
