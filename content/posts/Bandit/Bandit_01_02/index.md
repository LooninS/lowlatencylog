---
title: Bandit Level 1 → Level 2
date: 2026-06-11
tags: [bandit, linux, OTW, walkthrough]
description: Get the password from the file called '-'.
---
# Bandit Level 1 → Level 2
## Login
SSH: `ssh bandit1@bandit.labs.overthewire.org -p 2220`
Password: `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

---
## Task
Get the password from the file called `-`.

---
## A little bit of Theory
In Linux, arguments starting with a hyphen (`-`) are typically interpreted as command flags. To reference a file named literally `-`, I need to bypass the command’s option-parsing logic.

---
## Solution
First, I checked that the file existed:
```bash
bandit1@bandit:~$ ls
-
```
Using `cat -` will not work, because `-` is treated as standard input. I needed to give a relative path instead:
```bash
bandit1@bandit:~$ cat ./-
```
Alternatively, I could also use the option delimiter:
```bash
bandit1@bandit:~$ cat -- -
```
---
[next level](../../posts/bandit_02_03/)
