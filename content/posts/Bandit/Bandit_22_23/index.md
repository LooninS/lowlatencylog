+++
title = "Bandit 22 → 23"
date = 2026-06-19
description = "walkthrough of the Bandit 22 → 23"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level](../../posts/Bandit/bandit_21_22)

### Login
ssh: `ssh -p 2220 bandit22@bandit.labs.overthewire.org`
pass: `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`
***
### Level Goal

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.
***

### Solution
Let's start by checking the cronjob configuration:
```bash
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23 
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```
much like the previous level, the user `bandit23` will run a script at `/usr/bin/cronjob_bandit23.sh` and send all the output to a null device.

This tells us:
* The script runs as user bandit23
+ It executes every minute
- Both stdout and stderr are redirected to /dev/null

Now inspect the script:
```bash
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh 
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

Let's try to understand what the script does:
+ `whoami` → resolves to bandit23
+ `echo I am user $myname` → constructs a deterministic string
+ `md5sum` → hashes it
+ `cut` → extracts only the hash value
+ The script writes `/etc/bandit_pass/bandit23` to `/tmp/<hash>`

The main point is that the hash isn't random, it's completely dependent on the username.

Since the username is `bandit23`, we can evaluate the hash:

```bash
bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1   
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```
With that we have the password of the next level!
***
[next level](../../posts/Bandit/bandit_23_24)
