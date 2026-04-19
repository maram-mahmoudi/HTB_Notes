# Crocodile 

**Protocol/Topic:** FTP + Web Enumeration (PHP)  
**Ports:** 21 (FTP), 80 (HTTP)  
**Date Solved:** 15 April 2025  
**Difficulty:** Very Easy

---

## What I Learned

This machine combines two services, FTP and a web server. The idea is that the FTP server leaks credentials, and those credentials are used to log into an admin panel on the web server. It's a good example of how services on the same machine can be chained together.

---

## Enumeration

```bash
# Scan with default scripts and version detection
nmap -sC -sV <target-ip>
```

## FTP — Getting the Credentials

```bash
# Connect and try anonymous login first
ftp <target-ip>
# Username: anonymous
# Password: (blank)

# List files
ls

# Download files you find
get <filename>

# Disconnect
bye
```

The FTP server may have credential files (usernames, passwords) sitting in the public directory. Download and read them.

## Web Enumeration — Finding the Login Page

```bash
# Look for PHP pages specifically
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

# Alternative tools
dirb http://<target-ip>/ /usr/share/wordlists/dirb/common.txt -X .php

ffuf -u http://<target-ip>/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php
```

Once you find something like `login.php` or `admin.php`, open it in the browser:

```
http://<target-ip>/login.php
```

## HTTP Response Codes — What to Focus On

| Code | Meaning | Action |
|---|---|---|
| `200` | Page exists and accessible | Go look at it |
| `301/302` | Redirect | Follow it |
| `403` | Exists but forbidden | Note it, come back |
| `404` | Doesn't exist | Skip |

---

## The Attack Chain

1. Nmap → ports 21 and 80 are open
2. FTP anonymous login → find credential file(s)
3. Gobuster → find `login.php` or admin panel
4. Use credentials from FTP to log into the web admin panel

---

## Key Takeaway

Never look at services in isolation. An FTP server and a web server on the same machine are probably related. Files on FTP often feed into the web app credentials, config, uploads. Always enumerate all open ports and think about how they connect.
