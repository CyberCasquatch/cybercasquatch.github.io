---
layout: post
title:  "Credentials in Shares: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'credentials', 'shares', 'htb']
---

# Credentials in Shares

## Description

Credentials exposed in network shares are one of the most common misconfigurations in Active Directory environments, especially in medium to large enterprises. This issue also occurs in small businesses but on a lesser scale. The trend seems to have shifted from warnings like "Don't leave your password on a post-it note" to "Don't leave unencrypted credentials and tokens scattered everywhere."

Exposed credentials are often found within scripts and configuration files (e.g., batch files, cmd, PowerShell, config, ini). While credentials on a user's local machine are typically stored in text files, Excel sheets, or Word documents, the risk posed by network shares is significantly greater. These shares may be accessible by any user for several reasons:

- **Misconfigured permissions**: An admin user might initially set the correct permissions, but over time, the share becomes accessible to everyone, including domain users.
- **Unintentional exposure**: Admins often store test scripts in shared directories, unaware that they are making these files publicly accessible.
- **Forgotten open shares**: Sometimes, shares are created intentionally for data transfers but are forgotten about, leaving them open.
- **Hidden shares**: Shares with names ending in a dollar sign ($) are often assumed to be inaccessible, but they can still be discovered with the right tools, even if Windows Explorer does not display them.

> _Place screenshot of an exposed share (dev$)_ 

## Attack

The first step in identifying exposed credentials is discovering which shares exist in the domain. Tools like **PowerView's `Invoke-ShareFinder`** are useful here. This tool can list shares, filter out default ones (like c$ and IPC$), and check if the user has access to any of the found shares. The output will show shares that the current user can at least read.

In production environments, the number of shares can be enormous, requiring extended periods to scan. Here's an example of a hidden share, dev$, which, because of the dollar sign, is hidden in Windows Explorer.

> _Place screenshot of hidden share (dev$) showing empty list in Explorer_

However, even though the share is hidden in Explorer, you can still access it using the UNC path directly:

> _Place screenshot showing access to the share via UNC path_

We can use tools like **SauronEye** to search for specific words within files on the share. This tool can search through files to identify passwords or other sensitive data. However, for this walkthrough, we'll manually use `findstr`, a built-in Windows command, to search for exposed credentials. Here's how we would set up the `findstr` search:

```bash
findstr /s /i /m pass *.bat *.cmd *.ps1 *.conf *.ini
```
/s tells findstr to search the current directory and all subdirectories.
/i makes the search case-insensitive.
/m limits the output to filenames that contain a match.

*Place screenshot of running findstr on a directory*

In addition to common terms like "pass" and "pw", we can also search for the NetBIOS name of the domain, as certain commands may use it directly (e.g., runas, net), which might expose credentials in plaintext.

*Place screenshot of finding credentials with findstr*

Finally, be aware that using findstr in this manner can trigger alerts in security software, such as Windows Defender, for suspicious activity.

* * *

Prevention
----------

The key to preventing these kinds of attacks is to properly lock down shares within the domain. Ensure that no shares are left with overly permissive access. While it’s impossible to control everything users leave behind (e.g., hardcoded credentials in scripts), regular scans of the Active Directory environment can help identify open shares or older exposed credentials.

* * *

Detection
---------

Behavioral analysis of user activity can help detect when credentials exposed in shares are being exploited. Monitoring logon events (e.g., event IDs 4624, 4625 for successful and failed logons, and 4768 for Kerberos authentication) can indicate when someone is using exposed credentials.

*Place screenshot of event logs (4624, 4768) showing logon activity*

Another detection technique is to monitor for unusual connections. If a workstation is scanning hundreds or thousands of shares, it could indicate suspicious activity - for example, when `Invoke-ShareFinder` scans every domain device to obtain a list of its network shares.


* * *

Honeypot
--------

An effective method to detect abuse of exposed credentials is to set up a honeypot account. This is a semi-privileged account with a deliberately incorrect password. Here’s how you can set up the account:

1. Create a service account that was established over 2 years ago.
2. Ensure the password hasn't been changed in at least one year.
3. The file containing the fake password should be modified after the last password change.
4. The account should remain active in the domain.
5. The script containing the credentials should appear realistic (e.g., an MSSQL service account).

With this setup, if an adversary tries to use the credentials, it will trigger logon failures (event IDs 4625, 4771, and 4776), which can then be investigated.

**Insert screenshot of event IDs 4625, 4771, and 4776 logs here**

* * *

Additional Resources
--------------------

*   [vivami/SauronEye: Search tool to find specific files containing specific words, i.e. files containing passwords.](https://github.com/Vivami/SauronEye)
*   [Veil-PowerView/PowerView/functions/Invoke-ShareFinder.ps1 at master · darkoperator/Veil-PowerView](https://github.com/Veil-PowerView/Veil-PowerView/blob/master/functions/Invoke-ShareFinder.ps1)

#### QUESTIONS

### Connect to the target and enumerate the available network shares. What is the password of the Administrator2 user?
ANSWER:


<button onclick="history.back()">Go Back</button>
