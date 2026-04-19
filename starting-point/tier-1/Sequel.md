# Sequel

**Protocol/Topic:** MySQL  
**Port:** 3306  
**Date Solved:** 14 April 2025  
**Difficulty:** Very Easy

---

## What I Learned

This machine is about connecting directly to a MySQL database that's exposed on the network, no web app in the middle, just raw database access. The big lesson here: **never assume a service seen in Nmap is running on your own machine**. It's on the target.

Also learned that `-sC` in Nmap runs safe default scripts, which is useful for pulling service versions and basic info automatically.

---

## Enumeration

```bash
# Run safe scripts + version detection (-sC = default scripts, -sV = versions)
nmap -sC -sV <target-ip>

# If you want to be thorough, scan all ports
nmap -p- --min-rate 5000 <target-ip>
```

## Connecting to MySQL Remotely

**Important:** `mysql -u root -p` connects to your *local* machine by default. For a remote database you have to specify the host.

```bash
# Connect to remote MySQL (no password, don't use -p if the account has none)
mysql -u root -h <target-ip> -P 3306

# If it asks for a password and there isn't one, remove the -p flag entirely
# Using -p with no password makes the client hang waiting for input
```

## MySQL Commands Once Connected

```sql
-- List all databases
SHOW DATABASES;

-- Switch to a database
USE database_name;

-- List tables in current database
SHOW TABLES;

-- Read everything from a table
SELECT * FROM table_name;

-- Look at column names before dumping
DESCRIBE table_name;
```

---

## Common Mistakes to Avoid

| Mistake | Why it's wrong | Fix |
|---|---|---|
| `mysql -u root -p` without `-h` | Connects to local machine, not target | Add `-h <target-ip>` |
| Using `-p` when account has no password | Client will prompt and hang | Remove `-p` entirely |
| Stopping at `SHOW DATABASES` | The flag is usually inside a specific table | Keep going: USE → SHOW TABLES → SELECT |

---

## Key Takeaway

MySQL databases sometimes get left open to the network with weak or no credentials. The `root` user with no password is a common misconfiguration. Always try connecting without a password first before assuming auth is required.
