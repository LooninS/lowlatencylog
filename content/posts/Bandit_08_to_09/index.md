---
title: Bandit Level 8 → Level 9
date: 2026-06-13
tags: [bandit, linux, OTW, walkthrough]
description: The password for the next level is stored in the file data.txt and is the only line of text that occurs only once.
---
# Bandit Level 8 → Level 9
login
ssh: ssh bandit8@bandit.labs.overthewire.org -p 2220
password: `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`
***
## Level Goal
The password for the next level is stored in the file data.txt and is the only line of text that occurs only once.
***
## Approach
The password is repeated only once in the file. I need to just remove all the repeated lines.
So, I ran `uniq` on the `data.txt` file
```bash
bandit8@bandit:~$ uniq -u data.txt #-u or --unique only print unique lines
```
But wait... the output looked identical to just running `cat data.txt`. What's wrong?
Back to the manual I went, and there was the crucial detail I'd missed:
```markdown

DESCRIPTION
       Filter adjacent matching lines from INPUT (or standard input), writing to OUTPUT (or standard output).
```
And there is the issue, the `uniq` only works on lines next to each other, meaning if line above and below a line is different it's not omitted even if it's repeated elsewhere. I had to use `sort` to sort the lines in the file.

```bash
bandit8@bandit:~$ sort data.txt
```
Then I piped the output of `sort` to `uniq` 
```bash
bandit8@bandit:~$ sort data.txt | uniq -u
```
***
## Key Concepts

- **`uniq` command behavior**: Understanding that `uniq` only operates on *adjacent* lines is crucial. 
- **Combining `sort` and `uniq`**: To find truly unique lines in a file, `sort` must be used before `uniq` to bring identical lines together.
- **Piping for sequential processing**: This level further demonstrates the power of piping commands to process data sequentially, where the output of one command becomes the input of the next.
***
[next level](../../posts/bandit_09_to_10/)
