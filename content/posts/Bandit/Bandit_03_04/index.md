---
title: Bandit Level 3 → Level 4
date: 2026-06-13
tags: [bandit, linux, walkthrough, OTW]
description: The password for the next level is stored in a hidden file in the inhere directory.
---
# Bandit Level 3 → Level 4
## Login
SSH: `ssh bandit3@bandit.labs.overthewire.org -p 2220`
Password: `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`
***
## Level Goal
The password for the next level is stored in a hidden file in the inhere directory.
***
## Rambling
Some files are hidden from the user by default and start with `.`, like `.env` or `.bashrc`. We can view them in the terminal using the `ls -a` command.  
Like most things in Linux, the reason for their existence is legacy and has hardly anything to do with security.  
Early Unix directories included `.` for “current directory” and `..` for “parent directory,” and directory listings were adjusted to skip names starting with a dot so those special entries would not clutter the output. That convention accidentally created the modern “dotfile” behavior, where any filename beginning with `.` is hidden by default in tools like `ls` and many file managers.
***
## Solution
Use the `ls` command to list the files:
```bash
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ ls inhere

```
Hmm, there is nothing there... or the files are just hidden. Files starting with `.` are invisible to a normal `ls` listing.
```bash
bandit3@bandit:~$ ls -a inhere
.  ..  ...Hiding-From-You
bandit3@bandit:~$ cat inhere/...Hiding-From-You 
```
----
[Next Level](../../posts/bandit_04_05/)
