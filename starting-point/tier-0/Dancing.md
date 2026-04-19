# Dancing — 11.04.2026 

**Protocol:** SMB (Server Message Block)  
**Port:** 445  
**Difficulty:** Very Easy

---

## What I Learned

SMB is a communication protocol mainly used in Windows environments to share files, printers, and other resources across a network. It works as a **request-response** protocol ;your machine sends a request, the server responds.

The shared folders in SMB are called **shares**.

**Ports:**
- TCP 445 — modern SMB
- TCP/UDP 137, 138, 139 — older SMB over NetBIOS

### Why SMB Matters in Pentesting

SMB has a long history of serious vulnerabilities. A few things that make it interesting to enumerate:

- You can often **list shares without a password** using null sessions
- It can leak usernames, OS versions, and network info
- Some of the most famous exploits ever (like **EternalBlue**, which powered WannaCry ransomware) targeted SMBv1

---

## Enumeration

```bash
# Scan for SMB and get version info
nmap -sV -p 445 <target-ip>

# Run SMB-specific scripts
nmap -sC -sV -p 445 <target-ip>
```

## Listing and Accessing Shares

```bash
# List available shares (the -L flag = "list")
smbclient -L <target-ip>

# Connect to a specific share (no password = just hit Enter)
smbclient \\\\<target-ip>\\<share-name>

# Useful commands once inside
ls              # list files
cd <dir>        # change directory
get <file>      # download file
exit            # leave
```

---

## Key Takeaway

Always try null sessions on SMB. The `-L` flag on `smbclient` is your starting point. Some shares are publicly accessible with no credentials, and that's where you find the interesting stuff.
