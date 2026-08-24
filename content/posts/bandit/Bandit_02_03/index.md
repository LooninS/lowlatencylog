---
title: Bandit Level 2 → Level 3
date: 2026-06-13
description: Bandit Level 2 → Level 3
tags: [bandit, linux, walkthrough, OTW]
---
# Bandit Level 2 → Level 3
## Login
SSH: `ssh bandit2@bandit.labs.overthewire.org -p 2220`
Password: `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`
***
## Task
Get the password from the file called `--spaces in this filename--`.
***
This filename is a nightmare because it mixes hyphens and spaces. Nobody in their right mind does this, so use underscores like a sane person.

When I first tried to `cat --spaces in this filename--`, it failed immediately. The command interpreted `--spaces` as an option and treated the rest of the name (`in`, `this`, `filename--`) as separate files that do not exist.

To fix this, I needed to handle two separate issues:

- The **hyphen**: I used a relative path (`./`) so the shell would see the hyphen as part of a file path, not as an option flag.
- The **spaces**: I used quotes to group the words together, forcing the shell to treat the entire string as one filename.
---
## Solution
Like the previous level, the filename starts with `-`, so I needed to use a relative path. Otherwise:
```bash
bandit2@bandit:~$ cat --spaces in this filename--
cat: unrecognized option --spaces
Try 'cat --help' for more information.
```
However, it still failed when I used a relative path:
```bash
bandit2@bandit:~$ cat ./--spaces in this filename-- 
cat: ./--spaces: No such file or directory
cat: in: No such file or directory
cat: this: No such file or directory
cat: filename--: No such file or directory
```
This happened because the shell interpreted the command as four separate file names, all of which do not exist.

Instead, I needed to surround the filename with quotes, either single or double, so the shell would treat it as one name:
```bash
bandit2@bandit:~$ cat './--spaces in this filename--'
```
Alternatively, I could have used the option delimiter `--`, which tells the command to stop looking for flags:
```bash
bandit2@bandit:~$ cat -- '--spaces in this filename--'
```
Or I could let Tab completion handle the ugly escaping for me:
```bash
bandit2@bandit:~$ cat ./--spaces\ in\ this\ filename--
```
---
[Next Level](../../posts/bandit/bandit_03_04/)
