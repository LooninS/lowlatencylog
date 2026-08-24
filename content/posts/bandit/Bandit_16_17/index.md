+++
title = "Bandit 16 → 17"
description = "Walkthrough of the Bandit 16 → 17 challenge"
date = 2026-06-19
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
### Level Goal
The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. The task is to first identify which ports are listening, then determine which of those speak SSL/TLS, and finally find the one service that returns the next credentials instead of echoing the input back.
### Solution

The easiest way to identify open ports and check if they speak SSL/TLS is use `nmap` to scan the ports. However, I wanted to try completing this level using only Linux built-ins.

First, I need to find out which ports are open. I will use `nc` to do this.
```bash
nc -zv localhost 31000-32000 2>&1 | grep succeeded
```

On this level, that reveals the following open ports:

```text
Connection to localhost (127.0.0.1) 31046 port [tcp/*] succeeded!
Connection to localhost (127.0.0.1) 31518 port [tcp/*] succeeded!
Connection to localhost (127.0.0.1) 31691 port [tcp/*] succeeded!
Connection to localhost (127.0.0.1) 31790 port [tcp/*] succeeded!
Connection to localhost (127.0.0.1) 31960 port [tcp/*] succeeded!
```

At this point, the problem is which of these ports speak TLS. The `openssl s_client` is the right tool here because it attempts a TLS handshake and exposes certificate and protocol details when the target service really speaks SSL/TLS.

A small shell loop is enough to probe each candidate port:

```bash
for p in 31046 31518 31691 31790 31960; do
    cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:$p -quiet
done
```
Now the correct port will return the RSA key for login into next level.

> [!note]
>This level is a good illustration of layered reasoning in port analysis: first identify open ports, then classify protocol behavior, then validate application-level semantics.

>A port being open is only a transport-level fact; it says nothing yet about whether the service speaks plain text, TLS, or the correct challenge protocol.

>It is also a neat reminder that `nc` and `openssl s_client` complement each other well. `nc` is excellent for broad enumeration, while `openssl s_client` is better for interrogating encrypted endpoints in detail.
***
[next level >>>](../../posts/bandit/bandit_17_18)
