+++
title = "Bandit 15 → 16"
description = "Walkthrough of the Bandit 15 → 16 challenge"
date = 2026-06-19
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level <<<](../../posts/bandit_14_15)

### Level Goal
The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

**Helpful note:** Getting "DONE", "RENEGOTIATING" or "KEYUPDATE"? Read the "CONNECTED COMMANDS" section in the manpage.
***
### Solution
Unlike the previous level (port 30000 over TCP), port 30001 wraps the same service in **SSL/TLS**. I can't use `nc` here .
```bash
cat /etc/bandit_pass/bandit15 | ncat --ssl localhost 30001
```
Or, I can use `openssl` to connect to port 30001 over SSL/TLS.
```bash
cat /etc/bandit_pass/bandit15 | openssl s_client -connect localhost:30001 -quiet
```

The `-quiet` flag suppresses SSL handshake noise so you only see the server's response.
***
> [!note] `nc` vs `ncat`
> `ncat` is Nmap's rewrite of Netcat with SSL/TLS, proxy, and IPv6 support. If it's not available on your system, `openssl s_client` is a reliable fallback.
---

[next level >>>](../../posts/bandit_16_17)

