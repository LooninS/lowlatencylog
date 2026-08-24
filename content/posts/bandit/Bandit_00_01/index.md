---
title: Bandit Level 0 → Level 1
date: 2026-06-11
tags: [OTW, linux, walkthrough, bandit]
description: The password for the next level is stored in a file called readme located in the home directory.
---
# Bandit Level 0 → Level 1
[OverTheWire: Level Goal](https://overthewire.org/wargames/bandit/bandit1.html)
## Task
The password for the next level is stored in a file called **readme** located in the home directory.

---
## A little theory
When I SSH into a specific user, I land in that user’s home directory. This level focuses on learning basic Linux tools. In the terminal, `man` and the `--help` flag are going to be useful. Not all commands support both, so it is worth checking.

For this level and the subsequent ones, these are some of the prerequisite commands:

- `ls`: lists all unhidden files in a directory. I can use the `-a` flag to list all files, including hidden ones.
    
- `pwd`: shows the current working directory. Another way to tell is the prompt itself, for example `bandit0@bandit:~$`, where `~` indicates the home directory.
    
- `cat`: reads files sequentially and writes them to stdout, or in other words, prints the file contents to the console.
---
## Solution
I logged in with SSH and landed in the home directory of the `bandit0` user.

Then I used `ls` to confirm that the `readme` file existed:
```bash 
bandit0@bandit:~$ ls
readme
```
Finally, I printed the file contents with:
```bash
bandit0@bandit:~$ cat readme
```
[Next Level](../../posts/bandit/bandit_01_02/)

