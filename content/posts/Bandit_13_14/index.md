+++
title = "Bandit 13 to 14"
description = "Walkthrough for Bandit 13 to 14"
date = 2026-06-19
tags = ["linux","bandit","walkthrough","otw"]
+++
[<<< previous level](../../posts/bandit_12_13)

### Login
ssh: `ssh bandit13@bandit.labs.overthewire.org`
pass: `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`
***

### Level Goal
The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Look at the commands that logged you into previous bandit levels, and find out how to use the key for this level.
If you need help with this level: a hint file can be found in the home directory.
Make sure to read the error messages as they are informative.
***

### Solution

If I first login to bandit13, I can use the following command to login to bandit14:
```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org
```
But, bandit doesn't allow connections from localhost to conserve resources. I need to copy the `sshkey.private` to my machine and then do the login.

Using `scp` to copy the key to my machine:
```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private .
```
Enter the password for this level and I have the sshkey.private file on my machine.

Now just login to bandit14 using the key:
```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org
```

[next level](../../posts/bandit_14_15)
