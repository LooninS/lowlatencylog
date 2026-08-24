+++
title = "Bandit 27 → 28"
date = 2026-06-19
description = "walkthrough of Bandit 27 → 28"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level](../../posts/Bandit/bandit_26_27)

### Level Goal

There is a git repository at ssh://bandit27-git@bandit.labs.overthewire.org/home/bandit27-git/repo via the port 2220. The password for the user bandit27-git is the same as for the user bandit27.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

### Solution

```bash
bandit27@bandit:~$ git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
Cloning into 'repo'...
...
```
The password for repo is same as the level.

The password for the next level resides in ./repo/README

```bash
cd repo
cat README
```
***
[next level](../../posts/bandit/bandit_28_29) 

