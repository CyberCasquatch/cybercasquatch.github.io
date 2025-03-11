---
layout: post
title:  "Print Spooler & NTLM Relaying: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'ntlm', 'spooler', 'htb']
---

=============================

Description
-----------

The Print Spooler service has been a part of Windows for a long time and remains enabled by default, even in the latest Windows Desktop and Server versions. In 2018, Lee Christensen discovered an attack vector called **PrinterBug**, which exploits the functions `RpcRemoteFindFirstPrinterChangeNotification` and `RpcRemoteFindFirstPrinterChangeNotificationEx`. These functions can be leveraged to force a remote system to establish a connection to another machine, carrying authentication information in the form of a Ticket Granting Ticket (TGT). This means that any domain user can compel a remote server to authenticate to another machine of their choosing. Microsoft has classified this behaviour as "by design" and does not consider it a vulnerability requiring a patch.

The potential impact of PrinterBug is significant, particularly if the Print Spooler service is enabled on a Domain Controller (DC). The attack can be exploited in several ways:

*   Relaying the authentication request to another Domain Controller to perform a DCSync attack (if SMB signing is disabled).
*   Forcing the DC to connect to a server configured for Unconstrained Delegation, allowing attackers to extract the TGT from memory using tools like Rubeus or Mimikatz.
*   Relaying the authentication to an Active Directory Certificate Services (AD CS) server to obtain a certificate, enabling attackers to impersonate the DC.
*   Using the relay attack to modify Resource-Based Kerberos Delegation, allowing authentication as an administrator to a target machine.

Attack
------

In this scenario, we will exploit the vulnerability by relaying authentication to another Domain Controller and conducting a DCSync attack. For this method to work, **SMB Signing must be disabled** on the Domain Controllers.

### Step 1: Setting Up NTLMRelayx

First, we configure `NTLMRelayx` to relay connections to DC2 and execute the DCSync attack.

_(Insert your screenshot here)_

### Step 2: Triggering the PrinterBug

Next, we use `Dementor` on our Kali system to trigger the authentication request. If the attack is launched from a non-domain joined machine, valid domain credentials are required. In this case, we assume we have previously compromised a user account (e.g., Bob).

_(Insert your screenshot here)_

### Step 3: Confirming the Attack

Switching back to our NTLMRelayx session, we should now see that the DCSync attack has been successfully executed.

_(Insert your screenshot here)_

Prevention
----------

To mitigate this attack, the Print Spooler service should be **disabled on all servers** that do not require printing capabilities. This is especially important for Domain Controllers and other critical infrastructure servers.

If the service must remain enabled, you can reduce the risk by setting the registry key `RegisterSpoolerRemoteRpcEndPoint` to prevent remote connections:

    HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Policies\\Microsoft\\Windows NT\\Printers\\RegisterSpoolerRemoteRpcEndPoint = 2
    

Setting this value to `2` ensures that remote requests to the Print Spooler service are blocked.

Detection
---------

Detecting exploitation of PrinterBug can be challenging, as network connections to the Domain Controller may appear legitimate. However, monitoring for anomalies can help identify suspicious activity.

When using NTLMRelayx to perform a DCSync attack, event ID `4662` (which usually logs access to domain replication data) is **not generated**. However, an event for a successful logon from DC1 will be recorded, showing that authentication originated from the attacker's machine rather than another DC.

To detect this activity, organisations should correlate logon events from core infrastructure servers with their known static IP addresses.

_(Insert your screenshot here)_

Honeypot Strategy
-----------------

The PrinterBug can also be leveraged as a detection mechanism. By **blocking outbound connections** from servers to ports `139` and `445`, security teams can prevent attackers from exploiting the vulnerability while also using failed connection attempts as alerts for suspicious activity.

However, before implementing this strategy, it is crucial to ensure that legitimate network traffic is not disrupted. Core infrastructure components, such as Domain Controllers, require these ports to be open for replication.

While this honeypot approach may seem beneficial, organisations should be cautious. If a new, unknown vulnerability emerges that allows Remote Code Execution (RCE) without requiring a callback, attackers could bypass this control. Therefore, this method is best suited for mature security environments with robust monitoring and response capabilities.

_(Insert your screenshot here)_

### QUESTIONS

#### What is Kerberos des-cbc-md5 key for user Administrator?
ANSWER:

#### After performing the previous attack, connect to DC1 (172.16.18.3) as 'htb-student:HTB_@cademy_stdnt!' and make the appropriate change to the registry to prevent the PrinterBug attack. Then, restart DC1 and try the same attack again. What is the error message seen when running dementor.py?
ANSWER:


<button onclick="history.back()">Go Back</button>
