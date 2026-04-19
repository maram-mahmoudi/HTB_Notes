# Fawn - 10.04.2026 

**Protocol:** FTP (File Transfer Protocol)  
**Port:** 21  
**Difficulty:** Very Easy

---

## What I Learned

FTP is a protocol for transferring files between a client and server over a network. When a server is configured for **Anonymous FTP**, it lets anyone log in to a public directory without real credentials.

This is a big deal in pentesting because admins sometimes accidentally leave sensitive files in that public folder :config files, backups, notes. Even if files look harmless, the directory structure itself can reveal usernames or system layout.

**Anonymous login credentials:**
- Username: `anonymous`
- Password: anything (your email by convention, but blank works on most modern systems)

### Why Anonymous FTP Is a Security Risk

1. Admins may leave config files, backups, or notes in the public folder by accident
2. If the anonymous user has **write** access, an attacker could upload a web shell and execute it via a web server on the same machine
3. Even harmless files can leak directory structure and usernames

---

## Enumeration

```bash
# Basic port scan
nmap -sV <target-ip>

# Check if anonymous FTP login is allowed
nmap --script ftp-anon -p 21 <target-ip>
```

## FTP Login & Navigation

```bash
# Connect
ftp <target-ip>

# At the prompt, enter:
# Username: anonymous
# Password: (leave blank or type anything)

# Useful FTP commands once inside
ls              # list files
cd <directory>  # change directory
get <file>      # download a file to your local machine
put <file>      # upload a file
bye             # disconnect
```

---

## Key Takeaway

Anonymous FTP is low-hanging fruit in any pentest. Always check for it when port 21 is open. The real value isn't just getting in, it's what you find inside.
