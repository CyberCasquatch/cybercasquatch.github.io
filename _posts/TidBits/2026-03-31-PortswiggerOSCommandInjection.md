---
layout: post
title:  "Portswigger OS Command Injection"
date:   2026-03-31
author: C. Casquatch
category: TidBits
comments: false
tag: ['portswigger', 'burpsuite', 'command injection', 'appsec']
---

> Some notes on OS Command Injection: appsec

### OS command injection is a vulnerability that allows an attacker to execute operating system commands on the server running the application. It happens because the application takes user-supplied input and passes it directly to a shell command without any sanitisation. The shell then interprets any injected command separators as legitimate commands. This is dangerous because the commands that can be executed are virtually unlimited — from reading sensitive files to creating backdoors.

## Lab 1 — Basic OS Command Injection

The stock checker feature was vulnerable because it passed the productId and storeId parameters directly to a shell command without sanitisation.

Steps

1. Turn Burp Intercept on and click Check Stock on any product
2. The intercepted POST request body looks like:
```
productId=1&storeId=1
```

3. Modify either parameter to inject a command using a separator:
```
productId=1&storeId=1|whoami
```
or
```
productId=1|whoami&storeId=1
```
The & separator can also be used — since & has special meaning in URL encoding it needs to be encoded as %26:
```
productId=1%26whoami%26&storeId=1
```

Command Separators

| pipes the output of the first command into the next — whoami runs and its output is returned
& runs commands sequentially one after another
Both cause the shell to treat whoami as a separate command to execute

Reading the Output
Forward the request and the whoami output appears directly in the response where the stock number would normally be — returning the name of the current server user.

OS command injection happens because user input is passed directly to a shell without sanitisation. Applications should never construct shell commands using user-supplied input. Where OS commands are unavoidable, use safe APIs that pass arguments separately rather than as a single string, and apply strict allowlist validation on any user input.

<button onclick="history.back()">Go Back</button>
