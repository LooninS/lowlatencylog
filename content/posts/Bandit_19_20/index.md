+++
title = "Bandit 19 → 20"
date = 2026-06-19
description = "walkthrough of the Bandit 19 → 20"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level](../../posts/bandit_18_19)

### Login
ssh: `ssh -p 2220 bandit19@bandit.labs.overthewire.org`
pass: `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`
***
### LEVEL GOAL
To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

### Solution

Let's first find out what the setuid binary do:
```bash
bandit19@bandit:~$ ./bandit20-do 
Run a command as another user.
  Example: ./bandit20-do whoami
```

Aha, it allows me to run a command as user `bandit20`.

From the level goal, I know I need to get the password from `/etc/bandit_pass/bandit20`.

If I were to normally cat the file:
```bash
bandit19@bandit:~$ cat /etc/bandit_pass/bandit20
cat: /etc/bandit_pass/bandit20: Permission denied
```

I don't have permission to read the file.

Let's try it with `bandit20-do`:
```bash
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
```
It works! I got the password!
***

[next level](../../posts/bandit_20_21)


