---
layout: post
title:  "Credentials in Objects Properties: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'credentials', 'object properties', 'htb']
---

**Credentials in Object Properties**

Overview

Active Directory objects, such as user accounts, contain a variety of properties. Some of the typical attributes that can be found on a user object include:

* Account status (active or disabled)
* Expiration date of the account
* Date of last password change
* Username or account name
* Office location and contact information for the user

Administrators are responsible for populating these properties when creating accounts. In the past, some administrators would store sensitive information, such as passwords, within the Description or Info fields, assuming that only users with administrative privileges could view these properties. However, this assumption is incorrect, as domain users can typically read most properties of Active Directory objects, including Description and Info fields.

**The Attack**

Attackers can use a PowerShell script to query the Active Directory domain and search for specific strings or keywords (such as "pass" or "password") in these fields.

In this example, the script hunts for the term “pass” in the Description field of a user object, ultimately revealing the password Slavi123 associated with the bonni user account.

powershell

CopyEdit

# PowerShell script example

Get-ADUser -Filter \* -Property Description | Where-Object { $\_.Description -match "pass" }

[Insert screenshot showing PowerShell script execution and output]

**Prevention**

There are multiple ways to mitigate this vulnerability:

* Continuous Security Assessments: Regularly check for and detect any instances where credentials may be stored in object properties.
* Educate Privileged Users: Provide training to administrators and other high-privilege users to ensure they understand the risks of storing passwords in object properties.
* Automate User Creation: Where possible, automate the user account creation process to reduce the chance of administrators manually adding credentials to user objects.

[Insert screenshot of system setup for monitoring AD objects]

**Detection**

Detecting abuse of exposed credentials requires proper baselining of normal user behaviour. While it’s difficult to baseline regular user accounts, it's much easier to monitor and detect anomalous behaviour from administrative or service accounts.

For example, if credentials are being misused, you would likely see logon events such as:

* Event ID 4624 (successful logon)
* Event ID 4625 (failed logon)
* Event ID 4768 (Kerberos TGT request)

Below is an example of an event generated when a valid Kerberos TGT is requested:

[Insert screenshot of event ID 4768 log entry]

Unfortunately, when a user object is modified, the event ID 4738 is generated. However, this event does not provide any information about what specific property was changed or what the new values are. Thus, this event cannot be used to directly detect when credentials are added to an object.

**Honeypot Technique**

A practical honeypot technique involves using the Description field to store fake or incorrect credentials. This is especially effective in environments where basic security hygiene is lacking. Here’s how to set up a honeypot user:

1. Password in Description: Place a fake password in the Description field, as this is easily detectable by attackers.
2. Incorrect Password: The password placed in the Description field should not be valid.
3. Account Activity: Ensure the account has been recently active or has login attempts, making it look like a legitimate user.
4. Service Account: It’s more effective to use a service account rather than a regular user account, as these accounts are more often manually managed and are prone to misconfigurations.
5. Old Password: The password should appear as if it was set 2 or more years ago to make the fake credentials seem more believable.

Once configured, any attempts to authenticate using these invalid credentials will generate failed logon events. These events can be used to detect attackers attempting to access the account with the wrong password. Look for the following event IDs:

* 4625: Failed logon attempt
* 4771: Kerberos pre-authentication failure
* 4776: NTLM authentication failure

[Insert screenshot of failed logon events in security logs]

### QUESTIONS

#### Connect to the target and use a script to enumerate object property fields. What password can be found in the Description field of the bonni user?
ANSWER:

#### Using the password discovered in the previous question, try to authenticate to DC1 as the bonni user. Is the password valid?
ANSWER:

#### Connect to DC1 as 'htb-student:HTB_@cademy_stdnt!' and look at the logs in Event Viewer. What is the TargetSid of the bonni user?
ANSWER:




<button onclick="history.back()">Go Back</button>
