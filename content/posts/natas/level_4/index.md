+++
title = "Level 4 – Referer Spoofing"
date = 2026-06-25
description = "Natas level 4 writeup: abusing client-controlled Referer header to bypass origin-based access control."
tags = ["CTF", "web security", "overthewire", "natas", "HTTP headers", "referer spoofing"]
+++

## Initial Observation

As with previous levels, I started by inspecting the application via the browser’s developer tools (Network tab + HTML source). The hint on the page states:

![Hint](./devtools.png)

This strongly suggests that the server is making an authorization decision based on some notion of “where the user came from,” rather than on a proper authentication mechanism.

## Identifying the Attack Surface

Given the wording “users from …”, the natural question is: how can the server infer the origin of a request?

Typical options include:
- IP-based filtering (rare for this kind of CTF level).
- Cookies / sessions (not hinted at on the page).
- HTTP metadata such as the `Referer` or `Origin` header.

Opening the Network tab and looking at the request to `index.php`, the browser automatically sends a `Referer` header when navigating between Natas levels. That immediately connects the hint (“users from natas5”) to a likely implementation: a server-side check on the `Referer` value.

## How I Recognized Referer Spoofing

The reasoning path was:

- The hint talks about *where* the user is coming from, which maps naturally to the `Referer` header at the HTTP level.
- Natas levels often teach weak trust in client-controlled data (cookies, headers, query params), so headers are a prime suspect.
- Inspecting the actual request confirmed the presence of a `Referer` header pointing to the previous level’s URL.
- Since headers are fully under client control, “users from X” implemented via `Referer` implies that spoofing this header should bypass the check.

Putting this together, the most likely attack is to forge a request where `Referer` is set to `http://natas5.natas.labs.overthewire.org/`, causing the server to believe the request originates from the required level.

## Understanding the Referer Header

The HTTP `Referer` header contains the URL of the page that initiated the current request. Servers often use it for analytics, logging, or very weak access control. In this level, the server appears to be doing something conceptually like:

- If `Referer` contains `natas5.natas.labs.overthewire.org`, show the password.
- Otherwise, show an error message.

Because this header is part of the client’s HTTP request, it can be arbitrarily modified; it is not a trustworthy security signal.

One of the most basic concept of security, never trust the user input.

## Exploitation Strategy

The goal is to send a request to `natas4.natas.labs.overthewire.org` with a forged `Referer` header that matches the expected domain.

### Method 1: Browser DevTools

1. Open the `Network` tab in the browser’s developer tools.
2. Reload the page and locate the request to `index.php`.
3. Right-click the request and choose **Edit and Resend**.
4. Add or modify the `Referer` header to:

   ```http
   Referer: http://natas5.natas.labs.overthewire.org/
   ```

5. Send the modified request.

If the header matches what the server expects, the response now contains the password for the next level instead of the error.

### Method 2: Using curl

Alternatively, copy the request as cURL from devtools, keep the credentials, and inject the `Referer` header manually:

```bash
curl 'http://natas4.natas.labs.overthewire.org/index.php' \
  --compressed \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:148.0) Gecko/20100101 Firefox/148.0' \
  -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' \
  -H 'Accept-Language: en-US,en;q=0.9' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Referer: http://natas5.natas.labs.overthewire.org/' \
  -H 'DNT: 1' \
  -H 'Authorization: Basic bmF0YXM0OkpEclBudVpBS3lsNk1raXFRR0ZJZGRycXB2Z09BU3Ro' \
  -H 'Connection: keep-alive' \
  -H 'Upgrade-Insecure-Requests: 1' \
  -H 'Priority: u=0, i'
```

Here, the crucial part is the forged `Referer` header; the `Authorization` header is kept identical to what the browser originally sent, so the server still recognizes the Natas 4 credentials.
***
