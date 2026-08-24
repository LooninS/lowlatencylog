+++
title = "Bandit 25 → 26"
date = 2026-06-19
description = "walkthrough of Bandit 25 → 26"
tags = ["bandit", "walkthrough", "linux", "otw"]

+++
[previous level](../../posts/Bandit/bandit_24_25)

### Login

ssh: `ssh -p 2220 bandit24@bandit.labs.overthewire.org`  
pass: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

After solving the previous level we log in as `bandit24` and then grab the SSH key for `bandit26` from the home directory of `bandit25` (see previous post for that part). We’ll use that key to hit a weird, restricted shell as `bandit26`.

---

### Level goal

Instead of a normal shell like `/bin/bash`, `bandit26` uses a custom program as its login shell. The goal is to figure out what that program is, how it behaves, and how to escape from it into a real shell.

There’s also a warning in the original description: if you’re on Windows, Powershell can break the intended solution, so they recommend using Command Prompt for the SSH session.

---


Login as `bandit25` and inspect `/etc/passwd` to see what shell `bandit26` is configured to use:

```bash
bandit25@bandit:~$ grep bandit26 /etc/passwd
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

So instead of a normal shell we get `/usr/bin/showtext`. Let’s see what that actually does:

```bash
bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

This tells us a few important things:

- We’re dropped into a `/bin/sh` script, not directly into bash.  
- It hard-sets `TERM=linux`.  
- It then runs `more ~/text.txt` and replaces the shell with `more` (`exec`), so when `more` exits, the whole session ends.

So whatever we’re going to do as `bandit26` has to come from abusing `more`.


### Logging in as bandit26

From your local machine, use the SSH key you found in the previous level:

```bash
chmod 700 bandit26.sshkey
ssh -p 2220 -i bandit26.sshkey bandit26@bandit.labs.overthewire.org
```

If you do this with a normal-sized terminal, you’ll see `text.txt` flash and then the connection closes immediately. That’s because `more` is finishing instantly: the file fits on the screen, so `more` never enters interactive mode and the `exec`’d process exits.

`more` only becomes interactive if the content is longer than the terminal height. So we shrink the terminal window height before logging in, to something like 4–5 lines, so that `text.txt` no longer fits:

1. Make your terminal window small (less than 5 lines tall).  
2. Re-run the SSH command to log in as `bandit26`.  

Now `more` will show you the beginning of `text.txt` and a prompt at the bottom. From this interactive `more` session, we can use a built-in shortcut: pressing `v` launches a visual editor (typically `vi`/`vim`).

Press v
You should now be inside `vim`, editing `text.txt`.
Once we’re in `vim` as `bandit26`, we can ignore `text.txt` and just open the real password file directly. In vim, run:

```vim
:e /etc/bandit_pass/bandit26
```
This replaces the current buffer with the contents of `/etc/bandit_pass/bandit26`, which is the password for this level. 
We’re not done yet: we also want a real interactive shell as `bandit26`, not just the password. Vim can spawn a shell for us.
Inside vim, first make sure the `shell` option is set to `/bin/bash`:

```vim
:set shell=/bin/bash
```
Then start the shell:
```vim
:shell
```

This drops you into a `/bin/bash` running as `bandit26`. From here you have a normal shell and can explore the level environment just like in any other Bandit level.
***

[next level](../../posts/Bandit/bandit_26_27)
