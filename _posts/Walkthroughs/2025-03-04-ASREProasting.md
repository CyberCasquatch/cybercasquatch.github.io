---
layout: post
title:  "AS-REProasting: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'kerberoasting', 'htb']
---

# AS-REProasting Attack Walkthrough

## Description
The AS-REProasting attack is similar to the Kerberoasting attack; we can obtain crackable hashes for user accounts that have the property *Do not require Kerberos preauthentication* enabled. The success of this attack depends on the strength of the user account password that we will crack.

## Attack
To obtain crackable hashes, we can use Rubeus again. However, this time, we will use the `asreproast` action. If we don't specify a name, Rubeus will extract hashes for each user that has Kerberos preauthentication not required:

![Rubeus Command Example](path_to_your_screenshot.png)
*Insert screenshot showing Rubeus command output here*

Once Rubeus obtains the hash for the user Anni (the only one in the playground environment with preauthentication not required), we will move the output text file to a Linux attacking machine.

For `hashcat` to be able to recognize the hash, we need to edit it by adding `23$` after `$krb5asrep$`:

![Edit Hash for Hashcat](path_to_your_screenshot.png)
*Insert screenshot showing the edited hash here*

We can now use `hashcat` with the hash-mode (option `-m`) 18200 for AS-REPRoastable hashes. We also pass a dictionary file with passwords (the file `passwords.txt`) and save the output of any successfully cracked tickets to the file `asrepcracked.txt`:

![Hashcat Cracking Command](path_to_your_screenshot.png)
*Insert screenshot showing hashcat cracking command*

Once `hashcat` cracks the password, we can print the contents of the output file to obtain the cleartext password **Slavi123**:

![Cracked Password](path_to_your_screenshot.png)
*Insert screenshot showing cracked password here*

## Prevention
As mentioned before, the success of this attack depends on the strength of the password of users with *Do not require Kerberos preauthentication* configured.

First and foremost, we should only use this property if needed; a good practice is to review accounts quarterly to ensure that we have not assigned this property. Because this property is often found with some regular user accounts, they tend to have easier-to-crack passwords than service accounts with SPNs (those from Kerberoast). Therefore, for users requiring this configured, we should assign a separate password policy, which requires at least 20 characters to thwart cracking attempts.

## Detection
When we executed Rubeus, an Event with ID 4768 was generated, signaling that a Kerberos Authentication ticket was generated:

![Event ID 4768](path_to_your_screenshot.png)
*Insert screenshot showing Event ID 4768 here*

The caveat is that AD generates this event for every user that authenticates with Kerberos to any device; therefore, the presence of this event is very abundant. However, it is possible to know where the user authenticated from, which we can then use to correlate known good logins against potential malicious hash extractions. It may be hard to inspect specific IP addresses, especially if a user moves around office locations. However, it is possible to scrutinize the particular VLAN and alert on anything outside it.

## Honeypot
For this attack, a honeypot user is an excellent detection option to configure in AD environments; this must be a user with no real use/need in the environment, such that no login attempts are performed regularly. Therefore, any attempt(s) to perform a login for this account is likely malicious and requires inspection.

However, suppose the honeypot user is the only account with Kerberos Pre-Authentication not required. In that case, there might be better detection methods, as it would be very obvious for advanced threat actors that it is a honeypot user, resulting in them avoiding interactions with it.

To make a good honeypot user, we should ensure the following:

- The account must be a relatively old user, ideally one that has become bogus (advanced threat actors will not request tickets for new accounts because they likely have strong passwords and the possibility of being a honeypot user).
- For a service account user, the password should ideally be over two years old. For regular users, maintain the password so it does not become older than one year.
- The account must have logins after the day the password was changed; otherwise, it becomes self-evident if the last password change day is the same as the previous login.
- The account must have some privileges assigned to it; otherwise, it won't be interesting to try to crack its password's hash.

If we go back to our playground environment and configure the user 'svc-iam' (presumably an old IAM account leftover) with the recommendations above, then any request to obtain a TGT for that account should be alerted on.

![Honeypot Account Setup](path_to_your_screenshot.png)
*Insert screenshot showing honeypot account setup here*

### QUESTIONS

#### Connect to the target and perform an AS-REProasting attack. What is the password for the user anni?
ANSWER:

#### After performing the AS-REProasting attack, connect to DC1 (172.16.18.3) as 'htb-student:HTB_@cademy_stdnt!' and look at the logs in Event Viewer. What is the TargetSid of the svc-iam user?
ANSWER:


<button onclick="history.back()">Go Back</button>
