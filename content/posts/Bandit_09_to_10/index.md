---
title: Bandit Level 9 → Level 10
date: 2026-06-13
tags: [bandit, linux, otw, walkthrough]
description: The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’  characters.
---
# Bandit Level 9 → Level 10
## login
ssh: `ssh bandit9@bandit.labs.overthewire.org -p 2220`
password: `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`
***
## Level Goal
The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several  ‘=’  characters.
***
## Approach
My casual CTF practice suggested using the `strings` command to pull out only the readable text from the file..
```bash
bandit9@bandit:~$ strings data.txt
l0@P(
,k=?
tIsrQ
k`Dl5
....
```
The output was a bit overwhelming, so I decided to pipe the output to `grep` to filter out the lines that contained the password.
```bash
bandit9@bandit:~$ strings data.txt | grep '==='
```
***
## Key Concepts
- **Filtering Output with `grep`**: Combining `strings` with `grep` allows for efficient filtering of potentially large outputs, helping to pinpoint relevant information based on specific patterns.
---
[next level](../../posts/bandit_10_to_11/)
