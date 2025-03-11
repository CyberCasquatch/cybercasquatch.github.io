---
layout: post
title:  "Kerberos Constrained Delegation: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'kerberos', 'delegation', 'htb']
---

Kerberos Constrained Delegation
===============================

Description
-----------

Kerberos Delegation allows an application to access resources hosted on a different server. This enables scenarios where a service account can request access to another service on behalf of a user, rather than having direct access itself.

There are three delegation types in Active Directory:

*   Unconstrained Delegation (most permissive)
*   Constrained Delegation
*   Resource-based Delegation

While useful, delegation can be a security risk and should be minimised where possible. Unconstrained delegation allows access to any service, whereas constrained delegation restricts access to specified services. Resource-based delegation is defined within the target computer object and is less commonly seen in production.

Attack
------

Constrained delegation can be exploited by adversaries who gain control of a trusted account. They can request Kerberos tickets on behalf of any user and use them to access other services.

### Steps to Exploit Constrained Delegation:

1.  Identify accounts trusted for constrained delegation using PowerView’s `Get-NetUser` function.
2.  Obtain the NTLM hash of the compromised account’s password.
3.  Use `Rubeus` to generate a Kerberos ticket for an administrator account.
4.  Verify the ticket using the `klist` command.
5.  Use the ticket to access the Domain Controller via PowerShell Remoting.

Prevention
----------

Microsoft provides built-in protections, but they are not enabled by default. The following security measures should be taken:

*   Mark privileged accounts as _sensitive and not delegatable_.
*   Add privileged accounts to the **Protected Users** group to enforce security policies.
*   Ensure strong, cryptographically secure passwords for all delegated accounts.

Detection
---------

Monitoring user behaviour is crucial for detecting constrained delegation abuse. Security teams should:

*   Track logins and detect unusual access patterns.
*   Monitor Event ID **4624** for successful logons.
*   Inspect the **Transited Services** attribute in login events, which may reveal delegation activity.

Resources
---------

*   [Microsoft Archive - New-KrbtgtKeys.ps1](https://github.com/microsoftarchive/New-KrbtgtKeys.ps1)
*   [Mimikatz – Active Directory Security](https://adsecurity.org/?page_id=1821)
*   [PowerView - PowerSploit](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)

Challenge
---------

Use the techniques described to gain access to the `DC1` Domain Controller and submit the contents of the `flag.txt` file.

Screenshot Section
------------------

_(Insert your own screenshots here as you follow along with the process.)_

<button onclick="history.back()">Go Back</button>
