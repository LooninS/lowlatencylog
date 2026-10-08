+++
title = "Level 6"
date = 2026-06-26
description = "Natas Level 6: finding the secret through an exposed include file"
tags = ["otw", "natas", "web security", "file inclusion", "secrets"]
+++

Here’s a devlog‑style rewrite of your Level 6 post that keeps the technical content but reads more like a personal log and less like a repeated explanation.

---

## Natas 6 – devlog

This level presents a simple form: enter a “secret”, hit submit, and if it matches the server’s value, you get the natas7 password.

The interesting part isn’t the form itself, but the page source. Right at the top, there’s this:

```php
<?
include "includes/secret.inc";

if (array_key_exists("submit", $_POST)) {
    if ($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
}
?>
```

So the logic is:

- Include `includes/secret.inc`, which presumably defines `$secret`.
- On form submit, compare `$secret` with `$_POST['secret']`.
- If they match, reveal the next password.

That `include` line immediately raises a question: is `includes/secret.inc` itself accessible via HTTP?

Navigating directly to:

```text
http://natas6.natas.labs.overthewire.org/includes/secret.inc
```

shows the raw PHP source:

```php
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>
```

Now the solve is trivial:

1. Copy the value of `$secret`.
2. Paste it into the form’s “secret” field.
3. Submit → server prints the natas7 password.

---

[next page](../level_7/index.md)
