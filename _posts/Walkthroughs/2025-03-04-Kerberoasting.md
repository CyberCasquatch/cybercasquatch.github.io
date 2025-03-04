---
layout: post
title:  "Kerberoasting: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'kerberoasting', 'htb']
---

Kerberoasting
=============

Description
-----------

In Active Directory, a Service Principal Name (SPN) is a unique service instance identifier. Kerberos uses SPNs for authentication to associate a service instance with a service logon account, which allows a client application to request that the service authenticate an account even if the client does not have the account name. When a Kerberos TGS service ticket is asked for, it gets encrypted with the service account's NTLM password hash.

Kerberoasting is a post-exploitation attack that attempts to exploit this behavior by obtaining a ticket and performing offline password cracking to open the ticket. If the ticket opens, then the candidate password that opened the ticket is the service account's password. The success of this attack depends on the strength of the service account's password. Another factor that has some impact is the encryption algorithm used when the ticket is created, with the likely options being:

*   AES
*   RC4
*   DES (found in environments that are 15+ years old with legacy apps from the early 2000s, otherwise, this will be disabled)

There is a significant difference in the cracking speed between these three, as AES is slower to crack than the others. While security best practices recommend disabling RC4 (and DES, if enabled for some reason), most environments do not. The caveat is that not all application vendors have migrated to support AES (most but not all). By default, the ticket created by the KDC will be one with the most robust/highest encryption algorithm supported. However, attackers can force a downgrade back to RC4.

Attack Path
-----------

To obtain crackable tickets, we can use Rubeus. When we run the tool with the `kerberoast` action without specifying a user, it will extract tickets for every user that has an SPN registered (this can easily be in the hundreds in large environments):

![Kerberos attack with Rubeus](your-screenshot-path.jpg)

We then need to move the extracted file with the tickets to the Kali Linux VM for cracking (we will only focus on the one for the account Administrator, even though Rubeus extracted two tickets). We can use hashcat with the hash-mode (option `-m`) 13100 for a Kerberoastable TGS. We also pass a dictionary file with passwords (`passwords.txt`) and save the output of any successfully cracked tickets to a file called `cracked.txt`:

![Hashcat cracking TGS tickets](your-screenshot-path.jpg)

(If hashcat gives an error, we may need to pass `--force` as an argument at the end of the command.)

Once hashcat finishes cracking, we can read the file `cracked.txt` to see the password `Slavi123` in plain text:

![Cracked password from hashcat](your-screenshot-path.jpg)

Alternatively, the captured TGS hashes can be cracked with John The Ripper:

![John The Ripper cracking TGS hashes](your-screenshot-path.jpg)

Created directory: `/root/.john`  
Using default input encoding: UTF-8  
Loaded 3 password hashes with 3 different salts (krb5tgs, Kerberos 5 TGS etype 23 \[MD4 HMAC-MD5 RC4\])  
Node numbers 1-4 of 4 (fork)  
`Slavi123` (?))

Prevention
----------

The success of this attack depends on the strength of the service account's password. While we should limit the number of accounts with SPNs and disable those no longer used/needed, we must ensure they have strong passwords. For any service that supports it, the password should be 100+ random characters (127 being the maximum allowed in AD), which ensures that cracking the password is practically impossible.

There is also what is known as Group Managed Service Accounts (GMSA), which is a particular type of a service account that Active Directory automatically manages; this is a perfect solution because these accounts are bound to a specific server, and no user can use them anywhere else. Additionally, Active Directory automatically rotates the password of these accounts to a random 127 characters value. There is a caveat: not all applications support these accounts, as they work mainly with Microsoft services (such as IIS and SQL) and a few other apps that have made integration possible. However, we should utilize them everywhere possible and start enforcing their use for new services that support them to out-phase current accounts eventually.

When in doubt, do not assign SPNs to accounts that do not need them. Ensure regular clean-up of SPNs set to no longer valid services/servers.

Detection
---------

When a TGS is requested, an event log with ID 4769 is generated. However, AD also generates the same event ID whenever a user attempts to connect to a service, which means that the volume of this event is gigantic, and relying on it alone is virtually impossible to use as a detection method. If we happen to be in an environment where all applications support AES and only AES tickets are generated, then it would be an excellent indicator to alert on event ID 4769. If the ticket options are set for RC4, that is, if RC4 tickets are generated in the AD environment (which is not the default configuration), then we should alert and follow up on it. Here is what was logged when we requested the ticket to perform this attack:

![Event ID 4769 log](your-screenshot-path.jpg)

Even though the general volume of this event is quite heavy, we still can alert against the default option on many tools. When we run `Rubeus`, it will extract a ticket for each user in the environment with an SPN registered; this allows us to alert if anyone generates more than ten tickets within a minute (for example, but it could be less than ten). This event ID should be grouped by the user requesting the tickets and the machine the requests originated from. Ideally, we need to aim to create two separate rules that alert both. In our playground environment, there are two users with SPNs, so when we executed Rubeus, AD generated the following events:

![Rubeus ticket extraction event logs](your-screenshot-path.jpg)

Honeypot
--------

A honeypot user is a perfect detection option to configure in an AD environment; this must be a user with no real use/need in the environment, so no service tickets are generated regularly. In this case, any attempt to generate a service ticket for this account is likely malicious and worth inspecting. There are a few things to ensure when using this account:

*   The account must be a relatively old user, ideally one that has become bogus (advanced threat actors will not request tickets for new accounts because they likely have strong passwords and the possibility of being a honeypot user).
*   The password should not have been changed recently. A good target is 2+ years, ideally five or more. But the password must be strong enough that the threat agents cannot crack it.
*   The account must have some privileges assigned to it; otherwise, obtaining a ticket for it won't be of interest (assuming that an advanced adversary obtains tickets only for interesting accounts/higher likelihood of cracking, e.g., due to an old password).
*   The account must have an SPN registered, which appears legit. IIS and SQL accounts are good options because they are prevalent.

An added benefit to honeypot users is that any activity with this account, whether successful or failed logon attempts, is suspicious and should be alerted.

If we go back to our playground environment and configure the user `svc-iam` (probably an old IAM account leftover) with the recommendations above, then any request to obtain a TGS for that account should be alerted on:

![Honeypot detection setup](your-screenshot-path.jpg)

Be Careful!
-----------

Although we give examples of honeypot detections in many of the attacks described, it does not mean an AD environment should implement every single one. That would make it evident to a threat actor that the AD administrator(s) have set many traps. We must consider all the detections and enforce the ones that work best for our AD environment.

QUESTIONS

Connect to the target and perform a Kerberoasting attack. What is the password for the svc-iam user?
ANSWER:

After performing the Kerberoasting attack, connect to DC1 (172.16.18.3) as 'htb-student:HTB_@cademy_stdnt!' and look at the logs in Event Viewer. What is the ServiceSid of the webservice user?
ANSWER:

<button onclick="history.back()">Go Back</button>
