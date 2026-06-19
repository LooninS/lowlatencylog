---
title: Bandit 12 to 13
date: 2026-06-18
tags: [bandit, linux, reverse engineering, scripting]
desc: automating the process of solving bandit 12 to 13
---
# Bandit 12 to 13

## Login
- SSH: `ssh bandit12@bandit.labs.overthewire.org -p 2220`
- Password: `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

---

## Level Goal
The password for the next level is stored in `data.txt`. This file is a **hexdump** of a file that has been **repeatedly compressed** multiple times.

**Strategy hints from the level:**
- Create a working directory under `/tmp` (use a hard-to-guess name, or better: `mktemp -d`)
- Copy `data.txt` there using `cp`
- Rename it using `mv` (read the manpages!)

---

## My Approach

The last time I solved this level, I manually:
1. Reversed the hexdump
2. Repeatedly checked the filetype
3. Decompressed based on the filetype

I rather just not do that. Instead, I wrote a **bash script** to automate the entire process and spit out the password automatically.

---

## Writing the Bash Script

### What the Script Should Do

- Create a temporary folder, `cd` into it, and copy `data.txt` there
- Reverse the hexdump using `xxd -r`
- Loop:
  - Check the filetype
  - Decompress based on the filetype
  - Repeat until we get ASCII text

### The Script

```bash
#!/bin/bash

# Create temp folder and cd to it
WORKDIR=$(mktemp -d)
cd "$WORKDIR"

# Copy data.txt to current folder
cp /home/bandit12/data.txt datafile

# Reverse hexdump
xxd -r datafile > comp
rm datafile

while true; do
  FILETYPE=$(file -b comp)

  if echo "$FILETYPE" | grep -q "ASCII"; then
    cat comp
    break
  elif echo "$FILETYPE" | grep -q "gzip"; then
    mv comp comp.gz
    gunzip comp.gz
  elif echo "$FILETYPE" | grep -q "bzip2"; then
    mv comp comp.bz2
    bunzip2 comp.bz2
  else
    # Hardest part to debug and get working
    mv comp comp.tar
    tar -xf comp.tar
    rm comp.tar
    mv data*.bin comp
  fi
done

rm -rf "$WORKDIR"

# file -b: prints filetype without filename
# grep -q: doesn't print anything to stdout
```
> [!NOTE]  
> **Bandit doesn't allow executing arbitrary code in `home` or other restricted directories.**
> To run the script:
>```bash
>touch /tmp/bandit_12.sh
>chmod +x /tmp/bandit_12.sh
>/tmp/bandit_12.sh
>```
***
[Bandit 13 to 14](../../posts/bandit_13_14)


