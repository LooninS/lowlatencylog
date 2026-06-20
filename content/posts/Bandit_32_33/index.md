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

Immediately dropped into something unusual:

```bash
WELCOME TO THE UPPERCASE SHELL
>>
```

First instinct: try basic commands.

```bash
>> ls
sh: 1: LS: Permission denied
>> cat
sh: 1: CAT: Permission denied
>> cd
sh: 1: CD: Permission denied
```

Everything gets forced to uppercase before execution. So ls becomes LS, which doesn’t exist (or isn’t allowed). Same for every other command.

So the constraint is clear:
I can type lowercase, but execution happens in uppercase.

That means normal commands are useless. Need something that either:

+ doesn’t break when uppercased, or
+ bypasses the shell entirely

The second option feels more promising.

In most shells, `$0` refers to the current shell executable. If I can invoke it directly, I might escape whatever wrapper this “uppercase shell” is using.

```bash
>> $0
```
That works.

Now that I have a shell, I can invoke whatever I want.

```bash
$ whoami
bandit33
```

It’s pretty straightforward from here.
```bash
$ cat /etc/bandit_pass/bandit33
```
***
> [!tip] quick tip
>The shell isn’t restricting execution directly. It’s transforming input. That’s a weaker guarantee than it looks. Instead of blocking behavior, it relies on a naive assumption: that changing case breaks everything.

>But environment variables like $0 bypass that assumption entirely.

>This level is a good reminder:
>If input is modified but not controlled, look for things that survive the transformation.
***

It's the end of bandit for now.
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
