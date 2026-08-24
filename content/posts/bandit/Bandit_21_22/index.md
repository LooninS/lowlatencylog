+++
title = "Bandit 21 → 22"
date = 2026-06-19
description = "walkthrough of the Bandit 21 → 22"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
### Login
ssh: `ssh -p 2220 bandit21@bandit.labs.overthewire.org`
pass: `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`
***

### Level Goal
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.
***

### Solution
Let's first find out what the program do:
```bash
bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22  
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

So at every reboot, the user `bandit22` will run a script at `/usr/bin/cronjob_bandit22.sh` and send all the output to a null device.

Well, let's see what the script's upto:
```bash
bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh 
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Every reboot, a cronjob will run the script and the script will change permission of a temp file and write the next level password to it.

So, let's see what the temp file is:
```bash
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
With that we have the password of the next level!
***
[next level](../../posts/bandit/bandit_22_23)
