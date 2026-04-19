# HTB-Notes

I'm a student working my way through HTB machines to build real hands-on skills in cybersecurity. This repo documents every machine I solve, what I learned, what tripped me up, and the commands that mattered.

The goal isn't just to get flags. It's to actually understand *why* things work, and build a reference I can come back to.

---



## Structure

```
HTB-Notes/
├── starting-point/
│   ├── tier-0/          
│   └── tier-1/          
├── easy/                
├── medium/              
└── resources/           # Cheat sheets, tools, references
```

---

## Topics Covered So Far

- FTP enumeration and anonymous login
- SMB shares and null session enumeration
- Redis CLI interaction
- SQL injection (login bypass with comments)
- MySQL remote connection and enumeration
- Directory brute forcing (Gobuster, Dirb, ffuf)
- LFI and RFI vulnerabilities
- NTLM hash capture with Responder
- Password cracking with John the Ripper
- WinRM access with Evil-WinRM

---

## Tools I'm Using

`nmap` · `smbclient` · `redis-cli` · `mysql` · `gobuster` · `dirb` · `ffuf` · `responder` · `john` · `evil-winrm` · `ftp`

---

> Solving one machine a day. Learning something every time.
