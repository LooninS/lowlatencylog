---
title: Bandit 11 → 12
date: 2026-06-18
desc: walkthrough of bandit 11 → 12
tags: [bandit, walkthrough,linux]
---
# Bandit 11 → 12
## Login
- SSH: `ssh bandit11@bandit.labs.overthewire.org -p 2220`
- Password: `dtR173fZKb0RRsDFSGsg3RWnpNVj3qRr`

***

## Level Goal
The password for the next level is stored in the file `data.txt`, where all lowercase (`a-z`) and uppercase (`A-Z`) letters have been rotated by 13 positions.

***

## Solution
This level uses **ROT13**, a Caesar cipher with a shift of 13. Since the English alphabet has 26 letters, applying ROT13 twice returns the original text, which makes it useful for simple obfuscation but not for real security.

To solve the level, the contents of `data.txt` need to be translated by shifting each letter 13 positions forward or backward.

### ROT13 Alphabet Mapping

| Original | A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z |
|:--------:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Shifted  | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E | F | G | H | I | J | K | L | M |

### Using `tr` to Decrypt

The `tr` command translates one set of characters into another. Here, it maps `A-Za-z` to `N-ZA-Mn-za-m`, which performs the ROT13 transformation directly.

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

### How the Command Works

| Part | Meaning |
|------|---------|
| `tr` | Translates characters from one set to another |
| `'A-Za-z'` | Source set: all uppercase and lowercase letters |
| `'N-ZA-Mn-za-m'` | Target set: the same letters rotated by 13 positions |
| `< data.txt` | Sends the file contents to the command as input |

The mapping works like this:
- `A -> N`, `B -> O`, ..., `M -> Z`
- `N -> A`, `O -> B`, ..., `Z -> M`
- The same pattern applies to lowercase letters


>[!NOTE]
>ROT13 is often introduced as a toy cipher because it >is easy to reverse and simple to recognize. In this level, the key idea is not cryptography strength but being comfortable with small Unix text-processing tools.
***

[next level](../../posts/Bandit/bandit_12_13)
