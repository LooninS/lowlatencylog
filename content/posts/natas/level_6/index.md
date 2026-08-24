+++
title = "Level 6"
date = 2026-06-26
description = "Natas Level 6: finding the secret through an exposed include file"
tags = ["otw", "natas", "web security", "file inclusion", "secrets"]
+++

The page source reveals that the challenge hinges on a server-side comparison against a secret value:

```html
<?
include "includes/secret.inc";

if(array_key_exists("submit", $_POST)) {
    if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
}
?>
```

The critical detail is the `include "includes/secret.inc";` statement. This pulls in a separate file that defines the secret used by the form handler. Since the included file is publicly accessible, I inspected it directly and found:

```php
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>
```

Submitting this value through the form satisfies the equality check and returns the password for the next level.
![Screenshot](screenshot.png)

> [!tip]  
> This challenge demonstrates a common web security mistake: sensitive values should never be stored in files that are directly reachable from the web root. Even simple application logic can be bypassed if a secret is exposed to the client.
>
>The proper fix is to keep secrets outside the web-accessible directory and load them only on the server side. In general, secrets should be stored in protected configuration files or environment variables, and any exposed secret should be rotated immediately.

***

[next page](../level_7)
