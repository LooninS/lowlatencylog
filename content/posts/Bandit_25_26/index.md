+++
title = "Bandit 24 → 25"
date = 2026-06-19
description = "walkthrough of the Bandit 24 → 25"
tags = ["bandit", "walkthrough", "linux", "otw"] +++ [previous level](../../posts/bandit_23_24)
+++
[previous level](../../posts/bandit_23_24)
### Login
ssh: `ssh -p 2220 bandit24@bandit.labs.overthewire.org`
pass: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`
***

### Level Goal

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

    NOTE: if you’re a Windows user and typically use Powershell to ssh into bandit: Powershell is known to cause issues with the intended solution to this level. You should use command prompt instead.
***

### Solution

The shell of user is typically saved in `/etc/passwd`:
```bash
bandit25@bandit:~$ grep bandit26 /etc/passwd
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

We can see that the shell is `/usr/bin/showtext`. Let's check it out:
```bash
bandit25@bandit:~$ cat /usr/bin/showtext 
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

Great, we know have a small clue for the next level here. In other to login to bandit26:
```
chmod 700 bandit26.sshkey
ssh -p 2220 -i bandit26.sshkey bandit26@bandit.labs.overthewire.org
```
But the connection was closed by the server. This is because `more` isn't able to enter interactive mode. 
Before login, make the terminal window smaller, so less than 5 lines are displayed. This allows the more command to work.
Then press v and use the following command to check password of current level:
```bash
:e /etc/bandit_pass/bandit26
```

***
[Next level](../../posts/bandit_26_27)
