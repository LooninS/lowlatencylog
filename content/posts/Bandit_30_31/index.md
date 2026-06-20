+++
title = "Bandit 30-31"
date = 2026-06-19
+++
[<< Bandit 29-30](../../posts/bandit_29_30)
### Login 
password: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

### Solution

clone the repo
```bash
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo 
```
Check the README.md
```bash
cat README.md
just an epmty file... muahaha
```
That's seem to be....right. This truly is an empty file. There are no previous commits with leak or some other branch with the password. So, are we stump? Not quite. Git stores everything, so if the password was here we can find it. 

Git tags are used to mark specific commits in the repository, typically to identify releases or important milestones and unlike branches they don't move.

Let's check the tags
```bash
git tag
secret
```

We can see that there is a tag called secret. Let's checkout to that tag
```bash
git show secret
<password>
```

That's the password.
***
>[!info]
> Marks important points (releases like v1.0, milestones
> Tags don't move — unlike branches, they stay fixed to one commit 
>You can use `git show <tagname>` to see what it contains 
***
[Bandit 31-32>>](../../posts/bandit_31_32)
