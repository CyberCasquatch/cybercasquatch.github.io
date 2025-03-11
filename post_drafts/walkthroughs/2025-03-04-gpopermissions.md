---
layout: post
title:  "GPO Permissions/GPO Files: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'GPO Permissions', 'GPO files', 'htb']
---

# GPO Permissions/GPO Files Walkthrough

## Description
A Group Policy Object (GPO) is a container for policy settings that are assigned a unique identifier. It is one of the most commonly used tools for managing configurations within Active Directory. A GPO can contain one or more policy settings, and these settings are applied to objects within the Organizational Unit (OU) in the Active Directory hierarchy where the GPO is linked, as well as any child OUs beneath it. The scope of a GPO can be refined by using criteria like Active Directory groups (defaulting to Authenticated Users) or WMI filters, which can, for example, apply settings only to Windows 10 devices.

When a new GPO is created, typically, only users with administrative privileges, such as Domain Admins, are able to modify it. However, in many environments, delegation policies are in place that allow accounts with fewer privileges to edit these GPOs. This can become a security risk. For instance, if a GPO has permissions that allow modifications by broader groups such as 'Authenticated Users' or 'Domain Users', a compromised account could be used to alter the GPO. Potential changes might include adding malicious scripts or tasks that execute unwanted files. These modifications can lead to the compromise of all devices within the OUs linked to the vulnerable GPO.

Moreover, administrators often use GPOs to automate software installation or configure start-up scripts that are stored on network shares. If the permissions on the network share are improperly set, an attacker could replace the legitimate files with malicious ones. While the GPO itself might be correctly configured, misconfigured NTFS permissions on the shared files can open the door for attackers.

For more details on Group Policy Objects and how they are managed, visit [Microsoft's documentation](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-objects).

## Attack
No attack walkthrough is available here - it is a simple GPO edit or file replacement.

## Prevention
To reduce the risk of this type of attack, it is crucial to restrict GPO modification permissions to a specific group of users or even a single account. By doing this, the ability to edit or change GPO settings is tightly controlled, unlike scenarios where all Domain Admins can make modifications—this could easily extend to more than 50 users in some organizations. Additionally, it is important to avoid storing files in network shares that are accessible to a wide range of users, as this could allow unauthorized modifications to the file permissions.

It’s also a best practice to regularly review GPO permissions. This can be automated with scheduled tasks that run periodically, such as every hour, and send alerts if any unexpected changes to the permissions are detected.

## Detection
Fortunately, it is straightforward to detect when a GPO is modified. If Directory Service Changes auditing is enabled, then the event ID 5136 will be generated:

![Event ID 5136](path_to_your_screenshot.png)
*Insert screenshot showing Event ID 5136 for GPO modification*

From a defensive point of view, if a user who is not expected to have the right to modify a GPO suddenly appears here, then a red flag should be raised.

## Honeypot
Given the ease of detecting these types of attacks, some may consider using a misconfigured GPO as a honeypot, offering attackers an opportunity to exploit it. This can also be applied to files deployed via GPOs, where continuous monitoring is set up to detect any unauthorized changes (e.g., by checking file hashes). However, this tactic is only suitable for organizations that have a proactive security posture and the capability to quickly address critical vulnerabilities. If an escalation path is discovered through the manipulation of a GPO, the trap could backfire if not mitigated in real time, potentially becoming the weakest link in security.

When setting up a honeypot using a misconfigured GPO, consider the following precautions:

The GPO should only be linked to non-critical servers.
Set up continuous automation to monitor changes to the GPO. If the GPO is altered, the user responsible should be disabled immediately.
If a modification is detected, automatically unlink the GPO from all locations to prevent further exploitation.
To automate this process, PowerShell can be used. Below is an example script designed for this purpose. In our case, the honeypot GPO is identified by its unique GUID, and the script disables any account associated with modifying it. Since the script runs every 15 minutes as a scheduled task, it ensures that if multiple compromised users modify the GPO, all of them will be disabled. The script can send an email alert for notifications, but for demonstration, the output is shown on the command line:

```powershell
# Example PowerShell script for GPO modification detection and response
$GPO_GUID = "insert-GPO-GUID-here"
$logfile = "C:\path\to\logfile.txt"

# Monitor GPO modifications and disable compromised accounts
if (Test-Path $logfile) {
    $modifiedUsers = Get-Content $logfile
    foreach ($user in $modifiedUsers) {
        # Disable the compromised user account
        Disable-ADAccount -Identity $user
        Write-Host "User $user disabled due to GPO modification"
        # Optionally send an email alert here
    }
}
```

We will see the following output (or email body if configured) if the script detects that the honeypot GPO was modified:
*Insert screenshot showing event output when a user is disabled due to GPO modification*

As we can see above, the user bob was detected modifying our honeypot GPO and is, therefore, disabled. Disabling the user will then create an event with ID 4725:
*Insert screenshot showing Event ID 4725 for account disabled*


### QUESTIONS
From WS001 RDP again into DC1 (172.16.18.3) as 'htb-student:HTB_@cademy_stdnt!' and abuse GPO directly. Once completed type DONE as the answer



<button onclick="history.back()">Go Back</button>
