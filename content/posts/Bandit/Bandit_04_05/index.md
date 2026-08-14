---
title: Bandit Level 4 → Level 5
date: 2026-06-13
tags: [bandit, linux, walkthrough, OTW]
description: The password for the next level is stored in the only human-readable file in the inhere directory.
---
# Bandit Level 4 → Level 5
## Login
SSH `ssh bandit4@bandit.labs.overthewire.org -p 2220`
Password:  `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`
***
## Level Goal
The password for the next level is stored in the only human-readable file in the inhere directory. 
***
## Rambling
There are 10 files in the directory, but only one is human-readable, i.e. a text file.  
I have the option to use a brute-force approach to find the password by catting each file one by one, but that is not very efficient.  
It is much more efficient to use the `file` command to check the type of each file and then use `cat` to view the contents.  
`file` determines the file type.
***
## Solution
First, verify the files with `ls`:
```bash
bandit4@bandit:~$ ls inhere
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07	-file08  -file09
```
Get the file type with the `file` command:
```bash
bandit4@bandit:~$ file inhere/-file00
inhere/-file00: PGP Secret Sub-key -
```
Now, let’s perform the same operation on all the files in the directory. The `*` wildcard will match all the files in the directory:
```bash
bandit4@bandit:~$ file inhere/*
inhere/-file00: PGP Secret Sub-key -
inhere/-file01: data
inhere/-file02: data
inhere/-file03: data
inhere/-file04: data
inhere/-file05: data
inhere/-file06: data
inhere/-file07: ASCII text
inhere/-file08: data
inhere/-file09: data
```
Now, let’s use `cat` to view the contents of the human-readable file:
```bash
bandit4@bandit:~$ cat inhere/-file07
```
---
[Next Level](../../posts/Bandit/bandit_05_06/)
