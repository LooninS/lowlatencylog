---
title: OverTheWire Bandit level 0 Walkthrough
date: 2026-06-13
categories: [OverTheWire]
description: OverTheWire Bandit level 0 Walkthrough
tags: [OverTheWire, bandit, linux]
---
# OverTheWire Bandit level 0 Walkthrough
[OverTheWire: Level Goal](https://overthewire.org/wargames/bandit/bandit0.html)
## Introduction
[OverTheWire](https://overthewire.org/) is a great platform “to learn and practice security concepts in the form of fun-filled games.” It has several so-called “wargames,” each focused on a different area of security.

The recommended starting point is Bandit, which focuses on basic Linux and Git commands.

I have worked through it a couple of times and wanted to write this walkthrough to get more comfortable with writing and to keep a reference for future me. My thought process may differ from other authors, so it may also help someone else going through Bandit.

I will include small explanations of the topics, but it is best to do your own research, especially for something as hands-on as this.

----
## Task 

Login to level.  

Server: `bandit.labs.overthewire.org`
Username: `bandit0`
Password: `bandit0`

----
## Rambling 
*SSH (Secure Shell)* is a protocol for securely connecting to a remote machine over a network. SSH provides both encryption, which protects against eavesdropping, and authentication, which ensures that you are connecting to the intended server and not a malicious one.

On Linux, you can learn more about it by running `man ssh` in your terminal. SSH is a very common service and uses its own standard port: 22.

A port is like a specific dock at a busy ship port: the entire harbor is the computer, but each numbered dock is where one particular kind of ship, or service, is allowed to come in and offload or pick up cargo.

In CTFs, ports are useful because they tell you what services are running on the target machine.

When you see that port 21 is open, you think there is probably an FTP server there. Port 22 open means you can try logging in over SSH, and ports 80 or 443 usually hint at a web server waiting to be explored.

---

## Solution

The Basic `ssh` command:
```bash
ssh <username>@<server> -p <port>
```
The items in angle brackets are placeholders, which you replace with the information from the task.

Plugging in the given values:
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

That’s it. The task is simply to log in to the level.

**Note:** *The first time you connect, you will be prompted to verify the host key. This is how SSH helps prevent impersonation. *

---
## Bonus
I got tired of copy-pasting the same SSH command every time I switched levels, so I wrote a simple shell function to handle it. Now, instead of typing out the full command, I can just call this function.
```bash
# Add this to ~/.bashrc or ~/.zshrc 
ba() { ssh bandit$1@bandit.labs.overthewire.org -p 2220; }
```

Now, we can just use `ba <level>` to login. For example:
```bash
ba 1
```
This runs:
```bash
bandit1@bandit.labs.overthewire.org -p 2220
```
---
*[Level 1](../../posts/bandit/bandit_00_01/)*


