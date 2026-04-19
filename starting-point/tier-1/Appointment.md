# Appointment

**Protocol/Topic:** SQL Injection  
**Port:** 80 (HTTP)  
**Date Solved:** 13 April 2025  
**Difficulty:** Very Easy

---

## What I Learned

SQL injection is one of the most common and dangerous web vulnerabilities. It made the OWASP Top 10 in 2021 as **A03:2021-Injection**.

The idea is simple: a web app takes user input and plugs it directly into a database query without sanitizing it. If you're clever about what you type, you can manipulate the query logic entirely.

---

## How SQL Injection Works (Login Bypass)

A typical vulnerable login query looks like this:

```sql
SELECT * FROM users WHERE username = '<USER>' AND password = '<PASS>';
```

The app just glues your input into the query string. So if you control `<USER>`, you can break the SQL logic.

### Option 1: Comment out the password check

If you know the username is `admin`:

```
admin'--
```

The query becomes:
```sql
SELECT * FROM users WHERE username = 'admin'-- ' AND password = 'x';
```

Everything after `--` is a comment, so the password check is gone. You log in as admin without knowing the password.

> **MySQL note:** `--` needs a space after it to be treated as a comment. So `-- ` (with a space) or `#` both work in MySQL.

### Option 2: Always-true condition (when you don't know the username)

```
' OR 1=1-- 
```

The query becomes:
```sql
SELECT * FROM users WHERE username = '' OR 1=1-- ' AND password = 'x';
```

`OR 1=1` is always true, so the WHERE clause matches every row in the database. The app usually logs you in as the first user it finds ,often admin.

### When to use which

| Situation | Payload |
|---|---|
| You know the username | `admin'--` |
| You don't know any username | `' OR 1=1--` |

---

## Enumeration & Process

```bash
# Scan the web server — Apache 2.4.38 on Debian in this case
nmap -sC -sV <target-ip>

# Brute force directories on the web server
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# If you think there are PHP pages
gobuster dir -u http://<target-ip>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php
```

When you find a login page, open it in the browser and test your SQL injection payload in the username field.

---

## General Web Enumeration Workflow

When you see port 80/443/8080 open:

1. Open the IP in a browser first, see what the site looks like
2. Run `gobuster` or `dirb` to find hidden paths like `/admin`, `/login`, `/config`
3. Check the HTTP response codes:
   - `200` → page exists, go look at it
   - `301/302` → redirect, still worth following
   - `403` → exists but access denied (interesting)
   - `404` → doesn't exist

4. Look for input fields: forms, search boxes, URL parameters  and test them

---

## Key Takeaway

SQL injection in login forms is a classic. The `'` character is your probe,if the app breaks or behaves differently, the input isn't being sanitized. Comments (`--` or `#`) let you cut off the rest of the query. `OR 1=1` is your sledgehammer when you need a guaranteed match.
