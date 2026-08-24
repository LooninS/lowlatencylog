---
title: bandit level 6 → level 7
date: 2026-06-13
tags: [bandit, linux, walkthrough, otw]
description: the password for the next level is stored somewhere on the server and has all of the following properties.
---
# bandit level 6 → level 7
## login
ssh: ssh bandit6@bandit.labs.overthewire.org -p 2220
password: `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`
***
## level goal
the password for the next level is stored somewhere on the server and has all of the following properties:
- owned by user bandit7
- owned by group bandit6
- 33 bytes in size
---
## rambling
this is a fun one, instead of searching just one directory, i will have to search the entire file system. there is a minor problem though, i don't have the permissions to search the entire file system. so, when i use find command with the required parameters, i am flooded with permission denied errors.
```bash
bandit6@bandit:/$ find . -size 33c -group bandit6 -user bandit7
find: './root': permission denied
find: './proc/tty/driver': permission denied
find: './proc/1799527/task/1799527/fd/6': no such file or directory
find: './proc/1799527/task/1799527/fdinfo/6': no such file or directory
find: './proc/1799527/fd/5': no such file or directory
find: './proc/1799527/fdinfo/5': no such file or directory
find: './boot/lost+found': permission denied
find: './boot/efi': permission denied
find: './etc/polkit-1/rules.d': permission denied
find: './etc/sudoers.d': permission denied
find: './etc/xinetd.d': permission denied
find: './etc/credstore': permission denied
...
```
rather than dealing with all this noise, i decided to suppress the error output entirely. in bash, 2 is the error stream, 1 is the standard output stream and 0 is the standard error stream. `2> /dev/null`redirects stderr to /dev/null.
```bash
bandit6@bandit:/$ find . -size 33c -group bandit6 -user bandit7 2> /dev/null
./var/lib/dpkg/info/bandit7.password

bandit6@bandit:/$ cat ./var/lib/dpkg/info/bandit7.password
```
---
solution
the find command with size, group, and user flags locates the file, and error output is suppressed with 2> /dev/null:
```bash
bandit6@bandit:/$ find . -size 33c -group bandit6 -user bandit7 2> /dev/null
./var/lib/dpkg/info/bandit7.password

bandit6@bandit:/$ cat ./var/lib/dpkg/info/bandit7.password
```

## key concepts
file ownership and permissions: this level highlights the importance of file ownership (user and group) and how it can be used as a search criterion.
redirecting output: the 2> /dev/null command is a useful technique for redirecting error messages (stderr) to /dev/null, effectively silencing them and making the output of a command cleaner.

----
[next level](../../posts/bandit/bandit_07_to_08/)
