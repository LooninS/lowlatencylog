---
title: Bandit Level 10 → Level 11
date: 2026-06-18
tags: [bandit, linux, otw, walkthrough]
desc: Walkthrough for bandit 10 to 11

---
# Bandit Level 9 → Level 10
## login
ssh: `ssh bandit9@bandit.labs.overthewire.org -p 2220`
pass: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`
## Level Goal
The password for the next level is stored in the file data.txt, which contains base64 encoded data
***
## Approach
Generally a good to know if a file is base64 encoded is to look if it ends with equal signs. This isn't a guaranteed way to tell if the file is base64 encoded, but got to start somewhere.
```bash
base64 -d data.txt
```
---
[next level](../../posts/Bandit_11_12/)
