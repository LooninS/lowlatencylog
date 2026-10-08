+++
title = "Level 8"
date = 2026-10-08
description = "Natas Level 8"
tags = ["otw", "natas", "web security"]
+++

## Natas 8 – devlog

Like Level 6, this challenge presents a form that checks a “secret”. The difference is that the secret is no longer stored in plain text; it’s transformed before comparison.

Viewing the page source shows:

```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}

if (array_key_exists("submit", $_POST)) {
    if (encodeSecret($_POST['secret']) == $encodedSecret) {
        print "Access granted. The password for natas9 is <censored>";
    } else {
        print "Wrong secret";
    }
}
```

So the flow is:

- User submits a secret via `$_POST['secret']`.
- The server runs `encodeSecret()` on it:  
  `base64_encode → strrev → bin2hex`.
- The result is compared against `$encodedSecret`.

To win, I need an input whose encoded form equals `3d3d516343746d4d6d6c315669563362`. That means inverting the transformation:

```php
decodeSecret($encodedSecret) = base64_decode(strrev(hex2bin($encodedSecret)))
```

Running this on the command line:

```bash
echo "3d3d516343746d4d6d6c315669563362" \
  | xxd -r -p \
  | rev \
  | base64 -d
```

- `xxd -r -p` – hex → binary
- `rev` – reverse the byte string
- `base64 -d` – decode base64

The output is the plaintext secret. Submitting it in the form returns the password for natas9.

---

[next page](../level_9/index.md)
