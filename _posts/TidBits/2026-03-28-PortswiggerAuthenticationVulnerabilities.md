---
layout: post
title:  "Portswigger Authentication Vulnerabilities"
date:   2026-03-27
author: C. Casquatch
category: TidBits
comments: false
tag: ['portswigger', 'burpsuite', 'authentication', appsec]
---

> Some notes on authentication vulnerabilities: appsec

#### Authentication is the process of validating a user and their level of access. As a web vulnerability it can manifest as someone spoofing authentication, stealing credentials, or bypassing identity verification entirely. Authentication is supposed to validate that a person is who they say they are — if credentials can be stolen, guessed, or enumerated then that protection is bypassed.

## Lab 1 — Username Enumeration and Password Brute Force

### Concept

Username enumeration is the process of gathering valid usernames from a site. A site can accidentally reveal whether a username exists by responding differently depending on the input — for example returning "incorrect password" for a valid username but "username not found" for an invalid one. Even if the message looks similar, a difference in response length can reveal the same information.

### Process
Step 1 — Capture the Login Request

Open Burp Suite with the Proxy and Intercept turned on
Use Burp's built-in browser (Open Browser in the Proxy tab) as it routes traffic through Burp automatically
Navigate to the lab, enter any random username and password and click login
In Proxy → HTTP History find the POST request to /login and right-click → Send to Intruder

Step 2 — Enumerate the Username

In Intruder → Positions, click Clear § to remove all auto-detected markers
Highlight just the username value and click Add §:

username=§anything§&password=randompass

In the Payloads tab, select Simple list and paste in the username wordlist
Click Start Attack
Sort results by Length — one response will have a different length to all the others, indicating a different error message and therefore a valid username

Step 3 — Brute Force the Password

Go back to Intruder → Positions and fix the username to the one just found
Place the § markers around the password value instead:

username=validusername&password=§anything§

Paste in the password wordlist and click Start Attack
This time look for a 302 status code in the results — this indicates a successful login redirect while all failed attempts return 200

Step 4 — Log In
Use the discovered credentials to log in normally and access the account page.

Sites should always return identical error messages and response lengths regardless of whether the username or password was wrong — revealing which one failed gives attackers a foothold to enumerate valid usernames before brute forcing passwords. Rate limiting and account lockout policies are also critical defences against brute force attacks.

<button onclick="history.back()">Go Back</button>
