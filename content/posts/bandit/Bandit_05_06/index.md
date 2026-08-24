---
title: bandit level 5 → level 6
date: 2026-06-13
tags: [bandit, linux, otw, walkthrough]
description: find the password for the next level in the inhere directory.
---
# bandit level 5 → level 6
## login 
ssh: `ssh bandit5@bandit.labs.overthewire.org -p 2220`
password: `4oqyvpkxzooeoo5ptw81fb8j8lxxguqw`
***
## level goal
the password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable
---
## rambling 
In the last level, I used the `file` command to detect human-readable files.  
`grep` searches the given lines for a specific pattern defined by the user, or the opposite using the `-v` flag.  
the pipe `|` operator takes the output of one command and sends it as input to the next one. a command like `<command1> | grep <pattern>` is useful here because it lets me filter command output.

for finding non-executable files, i can use the `find` command. it has the `-executable` flag, which searches for executable files and can be negated with `!`.

other useful flags for `find`:

- `-type f` – find files.
- `-size 1033c` – find files that are 1033 bytes in size.
- `-executable` – find files that are executable.

i can also use the `-exec <command>` flag with `{}` as a path, which runs the chosen command on every matching file. that makes it easy to run `file` on the results of `find`.
## solution
the output of `find` can be piped to `grep` to filter the results, and the `-exec` option can be used to run `file` on each match:
```bash
bandit5@bandit:~$ find . -type f -size 1033c ! -executable -exec file {} + | grep "text"
./inhere/maybehere07/.file2: ascii text, with very long lines (1000)
```
this is probably the fastest and shortest solution to the level. still, i also wanted to try solving it using only the commands i already knew from the earlier levels, without relying too much on `find`.

The `-l` flag prints file permissions and `-r` flag execute `ls` recursively
the `inhere` directory has a bunch of subdirectories, and each one has multiple files. so i need a way to run `file` recursively on all of them, and i cannot simply use `*`.

the file should be:
 - non-executable, so there should be a `-` instead of `x` in the permissions.
- human-readable, so `file` should report `ascii text`.
- exactly 1033 bytes in size.

so i used `file` with globbing and piped the output to `grep`:
```bash
file */* {.,}* 2>/dev/null | grep "ascii text"
```
`*/` matches all subdirectories under `.`. the trailing `/` helps ensure i only match directories.  
`*` matches all visible files in the current directory, such as `file1`.  
`{.,}` is brace expansion:
- `{.,}` generates two strings: empty and `.`
- so `{.,}*` becomes `.*`, which matches hidden files or directories like `.file2`
together, `{.,}*` catches both visible and hidden files.

`2>/dev/null` cleans up errors. `2` is the code for `stderr`, and it sends its output to the null device.

now that i have narrowed down the sample size, i can run `ls -la` on the result from `file` and `grep`, one by one, to verify the file that is 1033 bytes and not executable:
```bash
ls -al ./inhere/maybehere07/.file2
```
---
[next level](../../posts/bandit/bandit_06_to_07/)
