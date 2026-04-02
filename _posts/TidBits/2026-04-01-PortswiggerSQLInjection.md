---
layout: post
title:  "Portswigger SQL Injection"
date:   2026-04-01
author: C. Casquatch
category: TidBits
comments: false
tag: ['portswigger', 'burpsuite', 'sql injection', 'appsec']
---

> Some notes on SQL Injection: appsec

### SQL injection is a vulnerability that allows an attacker to interfere with the queries an application makes to its database. The application is allowing user-supplied input to modify the structure of SQL queries, giving attackers the ability to view data that regular users cannot access. It is dangerous because an attacker can not only read sensitive data but also modify or delete it entirely.

## Lab 1 — SQL Injection in Category Filter

This lab was vulnerable in the product category filter. No Burp was needed — the injection was done directly in the URL.
Original Query
When clicking a category like "Gifts" the URL becomes:
```
/filter?category=Gifts
```

And the application runs:
```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
The `released = 1` condition is what hides unreleased products from users.

**The Injection**
By modifying the URL to:
```
/filter?category=Gifts'--
```

The ' closes the category string early and -- comments out everything after it, so the query becomes:
```
SELECT * FROM products WHERE category = 'Gifts'-- AND released = 1
```
The `AND released = 1` condition is completely ignored, returning all products including unreleased ones.

To return every product across all categories regardless of release status:
```
/filter?category='+OR+1=1--
```

Which produces:
```
SELECT * FROM products WHERE category = '' OR 1=1-- AND released = 1
```

Since 1=1 is always true, every product in the database is returned.

## Lab 2 — SQL Injection Login Bypass

This lab had a SQL injection vulnerability in the login form. The goal was to log in as the administrator without knowing the password.


Initial Gotcha — Client-Side Form Validation
Entering administrator'-- in the username field didn't work initially because the password field was marked as required in the HTML, so the browser wouldn't submit the form without something in it. Attempting to bypass via the URL (/login/administrator'--) also didn't work.
The key realisation was that the required validation is client-side only — the server doesn't actually need the password if the SQL injection comments it out. The fix was simply to type any random characters in the password field to satisfy the browser's form validation.

How the Injection Works
The original login query looks something like:
```
SELECT * FROM users WHERE username = 'administrator' AND password = 'yourpassword'
```

By entering administrator'-- as the username the query becomes:
```
SELECT * FROM users WHERE username = 'administrator'-- AND password = '...'
```

The -- comments out the entire password check, so the application logs in as administrator regardless of what password was entered.

Steps

1. Username: administrator'--
2. Password: any random characters (just to satisfy the browser's required field validation)
3. Click login — the password check is commented out and access is granted

SQL injection happens because user-supplied input is incorporated directly into SQL queries without sanitisation. Applications should use parameterised queries (also called prepared statements) which separate the SQL code from the data, making it impossible for user input to modify the query structure. Input validation and least-privilege database accounts are also important layers of defence.

<button onclick="history.back()">Go Back</button>
