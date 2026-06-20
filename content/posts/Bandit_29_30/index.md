+++
title = "Bandit 29 → 30"
date = 2026-06-19
description = "Walkthrough of Bandit 29 → 30"
tags = ["bandit", "walkthrough", "linux", "otw", "git"]
+++

[previous level](../../posts/bandit_28_29)

## Login Details

**Password:** `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`
***

## Level Goal

There is a git repository at
`ssh://bandit29-git@bandit.labs.overthewire.org/home/bandit29-git/repo`
via port `2220`.  
The password for `bandit29-git` is the same as for the user `bandit29`.
From your **local machine** (as the level text suggests) you’re supposed to clone this repository and find the password for the next level. In practice you can also do these git commands directly on the Bandit box as long as `git` is installed.
***

Start by cloning the repository. The password for the git account is the current level’s password.

```bash
# On your local machine:
$ git clone "ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo"
Cloning into 'repo'...
...

$ cd repo
$ ls
README.md
```
---

Check the only file in the repo:

```bash
$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>
```

Two important observations:

- We’re currently on the default branch, usually `master` or `main`, think of this as the **production branch**.  
- The line `<no passwords in production!>` is both a security best practice and a hint: if there are no passwords in production, they might be hiding somewhere *else* in the git history.

Before we start guessing, let’s confirm which branch we’re on:

```bash
$ git log -1
commit fa0b3448... (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Sun Jun 14 ...

    fix username
```

So `HEAD -> master` tells us our working copy is on `master` – the production branch.

---
“No passwords in production” strongly suggests we should look at **other branches**.

```bash
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```
- `master` is the local branch we’re on.  
- `remotes/origin/...` entries are **remote-tracking branches**, they mirror branches that exist on the server (`origin`).  
---

We don’t have a local `dev` branch yet, only `remotes/origin/dev`. There are two common ways to inspect it:

1. **Simple (detached HEAD, fine for read‑only):**
```bash
$ git checkout origin/dev 
# switched to branch 'dev'
$ cat README.md
...
```
2. **“Proper” local tracking branch (optional but good git hygiene):**

```bash
$ git checkout -b dev origin/dev
$ cat README.md
...
- password: <password>
```

In both cases, once you are at the `dev` commit, the `README.md` in that branch contains the actual password for `bandit30` instead of the `<no passwords in production!>` placeholder.

Copy that password and you’re ready for the next level.

---

[next level](../../posts/bandit_30_31)

