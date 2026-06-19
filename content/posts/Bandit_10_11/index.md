---
title: Bandit Level 10 → Level 11
date: 2026-06-19
tags: [bandit, linux, otw, walkthrough]
desc: 

---
# Bandit Level 9 → Level 10
## login
ssh: `ssh bandit9@bandit.labs.overthewire.org -p 2220`
pass: `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`
## Level Goal
The password for the next level is stored in the file data.txt, which contains base64 encoded data
***
## Approach
Generally a good to know if a file is base6y encoded is to look if it end with equal signs. This isnt a gurantee but still a good clue.
```bash
base64 -d data.txt
```
---
[next level](../../posts/bandit_11_to_12/)
