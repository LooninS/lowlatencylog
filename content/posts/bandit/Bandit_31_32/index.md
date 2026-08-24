+++
title = "Bandit 31 -> 32"
date = 2026-06-20
tags = ["bandit", "otw", "linux"]
+++
# login
SSH: We don't need SSH for this level!
password: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`
***
### level goal

There is a git repository at ssh://bandit31-git@bandit.labs.overthewire.org/home/bandit31-git/repo via the port 2220. The password for the user bandit31-git is the same as for the user bandit31.

From your local machine (not the OverTheWire machine!), clone the repository and find the password for the next level. This needs git installed locally on your machine.

### log

clone the repo and check the readme

```bash
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
```

```bash
cd repo
cat README.md 
─────┬──────────────────────────────────────────────────────────────────
     │ File: README.md
─────┼──────────────────────────────────────────────────────────────────
   1 │ This time your task is to push a file to the remote repository.
   2 │ 
   3 │ Details:
   4 │     File name: key.txt
   5 │     Content: 'May I come in?'
   6 │     Branch: master
   7 │ 
─────┴─────────────────────────────────────────────────────
```

So this level flips the usual pattern. Instead of pulling secrets, we need to push something valid to the remote.

create file:

```bash
echo "May I come in?" > key.txt
```
```bash
Tried adding it:
git add key.txt
The following paths are ignored by one of your .gitignore files:
key.txt
hint: Use -f if you really want to add them.
hint: Disable this message with "git config set advice.addIgnoredFile false"
```
So key.txt is explicitly ignored. That’s the actual obstacle here—not Git itself, but the repository configuration.

Checked `.gitignore`, and sure enough, the all `.txt` files are being filtered out.

At this point, there are two options:

+ Modify .gitignore (messy, unnecessary)
+ Force-add the file

```bash
git add -f key.txt
git commit -m "added key.txt"
git push
```

On push, it asks for the password for bandit31-git (same as bandit31). After authentication, the remote validates the commit and returns the password for the next level.

After entering the password, we get the password needed for the next level.
***
[next level](../../posts/bandit/bandit_32_33)

