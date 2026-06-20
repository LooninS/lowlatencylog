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
At first glance, nothing useful. No commits leaking data, no extra branches.

So if it’s not in the working tree, the next step is to think about what Git stores beyond files.

Git doesn’t just track the latest state. It keeps all objects: commits, trees, blobs, references.

Branches are one way to reference commits. But they’re not the only one.

Checked for tags:
```bash
git tag
secret
```

That’s interesting.

Tags are just pointers to specific commits, usually used for marking releases. Unlike branches, they don’t move. If something was hidden intentionally, a tag is a clean place to stash it.


```bash
git show secret
<password>
```

That's the password.
***
>[!info] 
>The trick here is realizing that “nothing in the repo” doesn’t mean “nothing in Git.”
> The working directory is just one view. Git’s object store can contain data that isn’t visible through:
> + current files
> + commit history you casually browse
> + branches
>Tags are easy to overlook because they’re used less frequently in basic workflows.
***
[Bandit 31-32>>](../../posts/bandit_31_32)
