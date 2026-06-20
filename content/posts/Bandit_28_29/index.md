+++
title = "Bandit 28 → 29"
date = 2026-06-19
description = "Walkthrough of Bandit 28 → 29"
tags = ["bandit", "walkthrough", "linux", "otw", "git"]
+++

[previous level](../../posts/bandit_27_28)

## Login Details
**Password:**`Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`
---

## Level Goal

There is a Git repository at:

```text
ssh://bandit28-git@bandit.labs.overthewire.org/home/bandit28-git/repo
```

on port `2220`.

The password for the user `bandit28-git` is the same as for the user `bandit28`.

From your **local machine** (or from the Bandit box using `localhost`), clone the repository and find the password for the next level. You need Git installed where you run the clone command.

---

## Solution

We start by cloning the repository. In my case, I’m doing this directly from the Bandit machine:

```bash
bandit28@bandit:~$ git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
Cloning into 'repo'...
...
```

The clone succeeds, so let’s inspect the repository:

```bash
cd repo
ls
README.md
```

The obvious first step is to read the `README.md`:

```bash
cat README.md
```

```text
     │ File: README.md
─────┼──────────────────────────────────────────────────────────────────
   1 │ # Bandit Notes
   2 │ Some notes for level29 of bandit.
   3 │ 
   4 │ ## credentials
   5 │ 
   6 │ - username: bandit29
   7 │ - password: xxxxxxxxxx
   8 │
```

So the structure looks promising ,there *is* a credentials section, but the password is scrubbed out as `xxxxxxxxxx`.

At this point, given that this is a Git repo, the natural suspicion is:  
“Maybe the real password existed in an earlier commit and was later ‘fixed’.”

---

## Inspecting Git history

Let’s look at the commit history:

```bash
git log
```

```text
commit 0ac19fdc3883754c4494d8596516787adff439a0 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Sun Jun 14 17:54:22 2026 +0000

    fix info leak

commit 0147528a5371a119f7e0f8b6b2be25b727b70cde
Author: Morla Porla <morla@overthewire.org>
Date:   Sun Jun 14 17:54:22 2026 +0000

    add missing data

commit 95861e241f1a363a2d0d39f05e9e5fc2cff84e5e
Author: Ben Dover <noone@overthewire.org>
Date:   Sun Jun 14 17:54:22 2026 +0000

    initial commit of README.md
```

We have three commits:

- `initial commit of README.md` – the first version of the file.
- `add missing data` – likely where credentials were introduced.
- `fix info leak` – this is very suspicious: “fix info leak” often means *remove secrets from history*.

The current `README.md` corresponds to the latest commit (`fix info leak`). To see what changed in a previous commit, we can use:

```bash
git show <commit-hash>
```
This shows the commit metadata plus the diff (line-by-line changes) introduced by that commit.

### Finding secrets

Let’s inspect the commit with message `add missing data`:

```bash
git show 0147528a5371a119f7e0f8b6b2be25b727b70cde

commit 0147528a5371a119f7e0f8b6b2be25b727b70cde
Author: Morla Porla <morla@overthewire.org>
Date:   Sun Jun 14 17:54:22 2026 +0000

    add missing data

diff --git a/README.md b/README.md
index 7ba2d2f..d4e3b74 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials
 
 - username: bandit29
-- password: <TBD>
+- password: <password>
```

A quick breakdown of the diff:

- Lines starting with `---` / `+++` show the old and new file paths.
- The `@@` hunk header (`@@ -4,5 +4,5 @@`) tells you which part of the file changed.
- Lines starting with `-` were removed.
- Lines starting with `+` were added.

Here we see that:

- The old version had `- password: <TBD>`
- The new version has `+ password: <password>`

So in this commit, the real password was added. A later commit (`fix info leak`) replaced it in the working tree with `xxxxxxxxxx`, but Git kept the history — and that’s exactly what we’re exploiting here.

The `<password>` in that diff is the password for `bandit29`.
***
>[!info] **Git never forgets by default.**
>Unless you explicitly rewrite history (e.g., with git filter-branch or git filter-repo), anything you commit (including secrets) can usually be recovered from the log. 
***
[next level](../../posts/bandit_29_30)
