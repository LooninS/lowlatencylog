+++
title = "Bandit 14 -> 15"
description = "Walkthrough of the Bandit 14 -> 15 challenge"
date = 2026-06-19
tags = ["bandit", "walkthrough", "linux", "otw"]
+++

### Level Goal
The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

### Solution

For this level, we need to send a request to port 30000 on localhost with the password of the current level and get the response.
From the last level we know the password for current level resides in `/etc/bandit_pass/bandit14`.

So now we can pipe the contents of `/etc/bandit_pass/bandit14` to port 30000 on localhost and get the response.

```bash
cat /etc/bandit_pass/bandit14 | nc localhost 30000
```

[next level >>>](../../posts/bandit_15_16)
