+++
title = "Bandit 20 → 21"
date = 2026-06-19
description = "walkthrough of the Bandit 20 → 21"
tags = ["bandit", "walkthrough", "linux", "otw"]
+++
[previous level](../../posts/bandit_19_20)

### Login
ssh: `ssh -p 2220 bandit20@bandit.labs.overthewire.org`
pass: `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`
***

### LEVEL GOAL
There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: Try connecting to your own network daemon to see if it works as you think
****

### Solution

Let's first find out what the setuid binary do:
```bash
bandit20@bandit:~$ ./suconnect 
Usage: ./suconnect <portnumber>
This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back.
```

This isn't that difficult to use, we just need a port number to connect to. 
This port needs to be listening and transmitting the password of current level.

We will use `nc` for transmitting and making a connection:
```bash
bandit20@bandit:~$ cat /etc/bandit_pass/bandit20 | nc -nlvp 1234 &[1] 38
```
The `&` at the end of the command will put the process in the background.

Now we can use `suconnect` to connect to the port:
```bash
bandit20@bandit:~$ ./suconnect 1234
```
With that we have the password of the next level! 
***

[next level](../../posts/bandit_21_22)
