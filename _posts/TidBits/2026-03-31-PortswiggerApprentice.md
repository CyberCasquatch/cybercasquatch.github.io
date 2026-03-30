---
layout: post
title:  "TB - Portswigger Path Traversal"
date:   2026-03-31
author: C. Casquatch
comments: false
tag: ['portswigger', 'burpsuite', 'path traversal', appsec]
---

> Some notes on path traversal: appsec

#### Path traversal is a web vulnerability that allows people to access files from a company's server by manipulating the web URL. This is done by traversing directories using ../ — the aim is to access files and directories stored outside of the web root folder.

## Lab1 - Basic Path Traversal
The first lab had no encoding protections, so the payload was straightforward. By manipulating the filename parameter of an image URL I could walk up the directory tree and access /etc/passwd:
```
/image?filename=../../../etc/passwd
```

## Lab2 - Bypass via URL Encoding
The second lab was stricter — the server detected ../ sequences and blocked them. However, the server was checking the raw input first and then URL-decoding afterward, which is the vulnerability.
By encoding ../ the filter doesn't recognise it as a traversal sequence and lets it pass. The application then decodes it back into a real ../ and uses it in the file path.

### What I tried initially:
```
/image?filename=%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd
/image?filename=%252e%252e%252f%252e%252e%252f%252e%252e%252fetc%252e%252e%252fpasswd
/image?filename=..%c0%af..%c0%af..%c0%afetc..%c0%afpasswd
/image?filename=..%ef%bc%8f..%ef%bc%8f..%ef%bc%8fetc..%ef%bc%8fpasswd
```

These all had typos — some had extra encoding, some not enough, and some used the encoded separator after etc instead of a plain /.

What ended up working was:
```
/image?filename=%252e%252e%252f%252e%252e%252f%252e%252e%252fetc/passwd
```

This worked because the lab had two decoding steps:
- First decode (filter layer): sees %2e%2e%2f — no literal ../ so the filter passes it
- Second decode (application layer): resolves %2e%2e%2f into ../ and uses it in the file path

Note: /etc/passwd didn't need encoding at all — which means that the filter was specifically looking for traversal sequences (../) rather than blocking all forward slashes.

The order of operations matters — a server that sanitises before decoding is vulnerable because encoded payloads bypass the filter and get decoded later by the application. Secure implementations should decode first, then sanitise — or better yet, use an allowlist of known valid filenames rather than trying to block traversal sequences.

<button onclick="history.back()">Go Back</button>
