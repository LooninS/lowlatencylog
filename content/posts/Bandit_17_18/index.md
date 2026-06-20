+++
title = "Bandit 17 → 18"
date = 2026-06-19
description = "There are 2 files in the home directory: passwords.old and passwords.new."
tags = ["bandit", "linux", "otw", "walkthrough"]
+++
[Back to Bandit 16 → 17](../post/bandit_16_17)                                                                  
## Level Goal
There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19
***

## Solution

The solution is to use the `diff` command to compare the 2 files:

```bash
diff passwords.old passwords.new
```
This will show you the differences between the 2 files.

From the level goal, we know that the password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new.

The second line of the output of `diff` is the line that has been changed between the 2 files.
***
[On to Bandit 18 → 19](../post/bandit_18_19)

