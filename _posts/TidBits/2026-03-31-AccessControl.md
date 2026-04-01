---
layout: post
title:  "Portswigger Access Control"
date:   2026-03-31
author: C. Casquatch
category: TidBits
comments: false
tag: ['portswigger', 'burpsuite', 'access control', appsec]
---

> Some notes on access control: appsec

#### Access control is the way in which a site is protected in terms of who can access which parts of it. Admins have access to admin pages, users have access to user pages. It becomes a vulnerability when users are able to access pages or functionality they are not supposed to — such as a regular user reaching an admin panel.

## Lab 1 — Unprotected Admin Panel (robots.txt)
The first lab had an admin panel with no access control protecting it at all — it just needed to be found. I tried common paths like /admin, /login/admin and /login/carlos with no luck.
The solution was navigating to /robots.txt. Websites use this file to tell search engine crawlers which pages not to index — but this inadvertently discloses sensitive paths. The file revealed the path /administrator-panel, which I navigated to directly and accessed the admin panel without any authentication.

## Lab 2 — Unprotected Admin Panel (Hardcoded in JavaScript)
This lab also had an unprotected admin panel but at an unpredictable URL, so robots.txt wasn't useful here.
Starting from the home page I viewed the page source and used Ctrl+F to search for keywords like admin, panel, and href. This led me to a <script> section where the admin panel URL was hardcoded. Even though the URL was obfuscated and unpredictable, the JavaScript was visible to all users regardless of their role — so navigating directly to that URL granted access to the admin panel.
This is a classic example of security through obscurity — hiding a URL doesn't protect it if it's leaked in client-side code.

## Lab 3 — Forgeable Cookie
This lab used a cookie to identify whether a user was an administrator. Logged in as wiener, I inspected the cookies in the Application tab in DevTools under Storage → Cookies and found a cookie with a value of Admin=false.
Changing this to Admin=true and navigating to /admin granted full access to the admin panel. The server was trusting the cookie value without any server-side verification — since cookies are client-controlled, any user can modify them.

Access control vulnerabilities often come down to the server trusting user-controllable input to make security decisions — whether that's a URL, a cookie, or a query parameter. Sensitive functionality should always be protected server-side regardless of whether it's linked in the UI, and access decisions should never be based on data the client can modify.

<button onclick="history.back()">Go Back</button>
