# Pass The Hash Attack

Il Pass-the-Hash (PtH) è una tecnica che consente di autenticarsi su un sistema remoto senza conoscere la password in chiaro, utilizzando direttamente l’hash NTLM dell’utente. <br>
È un metodo estremamente potente nel contesto del penetration testing perché **bypassa la necessità di conoscere la password vera e propria**, e permette comunque di accedere a servizi remoti che accettano l’autenticazione NTLM (come SMB o WinRM).

La capacità di catturare un hash NTLM è quindi fondamentale: esso rappresenta la “firma” digitale della password, e può essere utilizzato per ottenere l’accesso a risorse di rete, spostarsi lateralmente all'interno dell'infrastruttura e perfino compromettere interi domini Active Directory. <br>
A differenza delle password in chiaro, **gli hash NTLM spesso non vengono ruotati regolarmente** e, una volta ottenuti, **rimangono validi per lunghi periodi**, specialmente se associati ad account amministrativi.
