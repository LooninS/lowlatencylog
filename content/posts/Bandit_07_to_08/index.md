---
title: bandit level 7 → level 8
date: 2026-06-13
tags: [bandit, linux, walkthrough, otw]
description: The password for the next level is stored in the file data.txt next to the word millionth
---
# Bandit Level 7 → Level 8
## Login
ssh: `ssh bandit7@bandit.labs.overthewire.org -p 2220`
password: `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`
***
## Level Goal
The password for the next level is stored in the file data.txt next to the word millionth
***
## Approach

This one is pretty easy. I solved it using `grep`  
```bash
bandit7@bandit:~$ cat data.txt  | grep "millionth"
```
The `cat` command display all the contents of `data.txt`, then `|` operator takes the output of cat command and give it to `grep` command, which print the line with pattern 'millionth'

***

## Key Concepts
- **Piping (`|`)**: This level reinforces the concept of piping the output of one command as the input to another, a fundamental aspect of shell scripting for data processing.
- **`grep` for Pattern Matching**: The `grep` command is essential for searching and filtering text based on patterns, making it highly useful for extracting specific information from files.
---
[next level](../../posts/bandit_08_to_09/)
