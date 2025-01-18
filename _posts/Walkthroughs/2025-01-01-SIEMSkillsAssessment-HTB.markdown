---
layout: post
title:  "Security Monitoring & SIEM Fundamentals Skills Assessment"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> This walkthrough follows along with the SOC Tier 1 Analyst assessment, detailing the investigation steps as we review security events in a home-cooked SOC environment.

Introduction
------------

Congratulations! You've just been hired as a SOC Tier 1 analyst at Eagle. Today is your first day monitoring alerts and security events in a pre-configured dashboard. Here's what happened after meeting your senior analyst.

Key Insights from Senior Analyst
--------------------------------

Here are some key details from your meeting with the senior analyst regarding the environment:

*   The organisation has moved all hosting to the cloud; the old DMZ network is now shut down.
*   The IT operations team consists of four core members, all with high privileges in the environment.
*   The IT operations team often uses default administrator accounts despite warnings.
*   All endpoint devices are hardened based on CIS hardening baselines, with limited whitelisting.
*   Admin activities should only occur on Privileged Admin Workstations (PAWs).
*   The Linux environment has very little activity and uses sudo for admin rights escalation.
*   Strict naming conventions are followed for service accounts, which contain '-svc' and are created with complex passwords.

If you are starting with a running instance, ensure to reset the target by clicking the "Reset Target" icon to regain access to the preconfigured dashboard.

Step 1: Access the Dashboard
----------------------------

Now that you're ready, navigate to the target's IP address to access the SOC dashboard: **http://\[Target IP\]:5601**. Click on the side navigation toggle and select "Dashboard". Here, you will review the "SOC-Alerts" dashboard.

### Visualization 1: Failed Logon Attempts (All Users)

This visualisation might help you spot potential brute force attacks. Look for any users with numerous failed attempts or multiple users trying to log in from the same endpoint device. At first glance, no significant issues arise, but an anomaly related to the "sql-svc1" account is noteworthy.

**Insert Screenshot 1: Failed Logon Attempts (All Users)**

### Visualization 2: Failed Logon Attempts (Disabled User)

Here, you notice that the user "Anni" attempted to authenticate despite their account being disabled.

**Insert Screenshot 2: Failed Logon Attempts (Disabled User)**

Step 2: Investigate Admin Logins
--------------------------------

### Visualization 3: Failed Logon Attempts (Admin Users Only)

For this visualisation, ensure to check if all events took place on Privileged Access Workstations (PAWs) or Domain Controllers. Admin login attempts should only occur on these machines.

**Insert Screenshot 3: Failed Logon Attempts (Admin Users Only)**

### Visualization 4: RDP Logon for Service Account

Service accounts are set up with a specialised function. Is there anything suspicious about the RDP login here? Examine closely.

**Insert Screenshot 4: RDP Logon for Service Account**

Step 3: Investigate Group Modifications
---------------------------------------

### Visualization 5: User Added or Removed from a Local Group

An administrator has added a user (represented only by their SID value) to the "Administrators" group. Should this be escalated to a Tier 2/3 analyst or should you consult with IT Operations first?

**Insert Screenshot 5: User Added or Removed from a Local Group**

### Visualization 6: Admin Logon Not From PAW

As per the security policy, administrators should only log in remotely from PAWs. If there’s a login outside of this, should you escalate it or consult IT Operations first?

**Insert Screenshot 6: Admin Logon Not From PAW**

Step 4: Review SSH Logins
-------------------------

### Visualization 7: SSH Logins

Remember that the root user account should not be used regularly. Is there anything unusual in the SSH login records?

**Insert Screenshot 7: SSH Logins**

### Questions:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "Failed logon attempts [All users]" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "Failed logon attempts [Disabled user]" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "Failed logon attempts [Admin users only]" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "RDP logon for service account" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "User added or removed from a local group" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "Admin logon not from PAW" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Review the "SSH Logins" visualization of the "SOC-Alerts" dashboard. Choose one of the following as your answer: "Nothing suspicious", "Consult with IT Operations", "Escalate to a Tier 2/3 analyst"
ANSWER:


<button onclick="history.back()">Go Back</button>
