+++
title = "Bandit 18 → 19"
description = "Walkthrough of the Bandit 18 → 19 challenge"
date = 2026-06-19
tags = ["linux", "walkthrough","bandit","otw"]
+++
[Previous level <<](../../posts/bandit_17_18)
## Level Goal
The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.
***
## Login
ssh: ssh -p 2220 bandit18@bandit.labs.overthewire.org
pass: x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
## Solution

When you log in with SSH, you are logged out, as the .bashrc file is modified to log you out.

I get logged out before I am able to anything. All I need to do is login and cat the readme file.

```bash
ssh bandit@bandit.labs.overthewire.org
cat ~/readme
```

But before I can cat the readme file, I get logged out. So, I should send the cat command with the SSH command:

```bash
ssh bandit@bandit.labs.overthewire.org "cat readme"
```
While this might sound weird, the man page for ssh says:
> [!info] ssh
If a command is specified, it will be executed on the remote host instead
of a login shell.  A complete command line may be specified as command,
or it may have additional arguments.  If supplied, the arguments will be
appended to the command, separated by spaces, before it is sent to the
server to be executed.
***
[Next level >>](../../posts/bandit_19_20)

