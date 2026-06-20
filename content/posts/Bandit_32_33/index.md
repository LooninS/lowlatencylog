+++
title = "Bandit Level 32 → Level 33"
date = 2026-06-19
description = "Level 32 → Level 33"
tags = ["bandit", "otw", "linux", "walkthrough"]
+++
[<< Bandit 31-32](../../posts/bandit_31_32)

### Login 
ssh: ssh bandit32@bandit.labs.overthewire.org -p 2220
password: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`
***

### Level Goal
After all this git stuff, it’s time for another escape. Good luck!
***

### log

Immediately we are dropped into uppercase shell.

```bash
WELCOME TO THE UPPERCASE SHELL
>>
```

No commands are working not `ls`, not `cat` and not `cd`.

```bash
>> ls
sh: 1: LS: Permission denied
>> cat
sh: 1: CAT: Permission denied
>> cd
sh: 1: CD: Permission denied
```

So, now what?

Let's start a new shell

```bash
>> $0
$ whoami
bandit33
$ cat /etc/bandit_pass/bandit33
```

With this we are dropped into a new shell and then run `/bin/sh` for bash shell and we are on the `bandit33`
***

For now this is the last level.
```bash
bandit33@bandit:~$ cat README.txt 
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```
***
