# Redeemer 

**Protocol:** Redis  
**Port:** 6379  
**Date Solved:** 12 April 2025  
**Difficulty:** Very Easy

  
---

## What I Learned

Redis (REmote DIctionary Server) is an open-source, **in-memory, key-value data store**. It's used as a database, cache, or message broker. Because it keeps data in RAM, it's extremely fast as in sub-millisecond latency.

It's a NoSQL database, meaning data isn't organized in tables like regular SQL. Instead, data is stored as key-value pairs (think of it like a dictionary: key → value).

The big security issue: **Redis instances are sometimes left exposed on the network with no authentication required**, which means anyone can connect and read/dump everything in the database.

---

## Enumeration

```bash
# Scan all ports (Redis runs on 6379 by default)
# Use --min-rate to speed up the full port scan
nmap -p- --min-rate 5000 <target-ip>

# Run safe scripts + service detection
nmap -sC -sV -p 6379 <target-ip>
```

## Connecting and Interacting with Redis

```bash
# Connect to remote Redis instance (no auth)
redis-cli -h <target-ip>

# Once inside, useful commands:
info            # get server info and stats — version, memory, connected clients, etc.
keys *          # list all keys in the database
select <n>      # switch to database number n (Redis has 0-15 by default)
get <key>       # get the value of a key
type <key>      # check what data type a key holds
```

### What `info` returns (important sections)
- `server` — Redis version, OS, uptime
- `clients` — number of connected clients
- `memory` — RAM usage
- `keyspace` — which databases have keys and how many

---

## Key Takeaway

Redis doesn't require auth by default — that's a misconfiguration that shows up a lot in real environments. Once you're in, `info` gives you server details, and `keys *` dumps everything stored. Always check if port 6379 is open and try connecting without a password.
