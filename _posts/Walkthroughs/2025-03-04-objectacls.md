---
layout: post
title:  "Object ACLs: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'object', 'access control list', 'acl', 'htb']
---

Description
-----------

In Active Directory, Access Control Lists (ACLs) define who can access a specific object and what type of access they have. These lists consist of Access Control Entries (ACEs), each specifying a security principal (user, group, or session) and its corresponding permissions.

ACLs also play a role in auditing by tracking access attempts and the types of access performed. A common example is Domain Admins having the default right to modify passwords. However, specific rights can be delegated to users or groups for tasks such as resetting passwords or modifying group memberships.

In large organizations, unintended delegation can occur, leading to security risks. Common misconfigurations include:

*   All Domain Users added as Administrators to all Servers.
*   Everyone having full modification rights over all objects.
*   All Domain Users able to view extended properties, such as LAPS passwords.

Attack
------

To identify vulnerable ACLs, we use [BloodHound](https://github.com/SpecterOps/BloodHound-Legacy) for visualization and [SharpHound](https://github.com/SpecterOps/SharpHound) for data collection.

By scanning the environment and passing `-c All` to SharpHound, we generate a ZIP file that BloodHound can analyze.

Consider an example where the user **Bob** has full control over another user, **Anni**, and a computer, **Server01**. Bob can exploit this in two ways:

*   **Case 1: Full control over Anni** - Bob can modify Anni's account, add a Service Principal Name (SPN) value, and conduct a Kerberoasting attack. Alternatively, he can reset Anni’s password and assume her identity.
*   **Case 2: Full control over Server01** - If LAPS is used, Bob can retrieve the stored local admin password. He can also abuse Resource-Based Kerberos Delegation to escalate privileges.

For further ACL analysis, we can use [ADACLScanner](https://github.com/canix1/ADACLScanner) to generate detailed reports on DACLs and SACLs.

Prevention
----------

*   Regularly assess Active Directory for misconfigured ACLs.
*   Train privileged users to avoid unsafe delegations.
*   Automate access management and restrict privileged access to administrative accounts.

Detection
---------

AD modifications generate security events, though they lack granular detail. Key event IDs include:

*   **4738** - A user account was changed (e.g., Bob adding an SPN to Anni).
*   **4724** - An account’s password was reset.
*   **4742** - A computer account was modified.

Organizations can use naming conventions (e.g., privileged users start with "adminxxxx") to flag unauthorized modifications.

Honeypot
--------

Misconfigured ACLs can also serve as tripwires. Two honeypot techniques include:

1.  Assigning high privileges to a fake user account with exposed credentials.
2.  Creating a user with deliberately weak ACLs and monitoring unauthorized modifications.

For example, any event ID 4738 triggered by a honeypot user should generate an alert.

Example Screenshots
-------------------

_Insert screenshots of SharpHound data collection, BloodHound analysis, and event logs here._

#### Repeat the example in the section and type DONE as the answer when you are finished

<button onclick="history.back()">Go Back</button>
