---
layout: post
title:  "Nessus Vulnerability Management Lab"
date:   2024-03-21
author: C. Casquatch
comments: false
tag: ['nessus', 'SIEM', 'lab', 'SOC']
---

> We will be using Nessus Essentials to scan local VMs hosted on VMWare Workstation in order run credentialed scans to discover vulnerabilities, remediate some of the vulnerabilities, then perform a rescan to verify remediation.

##### Languages and Utilities Used
* PowerShell
* VMware Workstation

##### Environments Used
* Windows 10
* Nessus Essentials

### Program walk-through:

#### Turning off the firewall and creating a scan for the host
# photo1

#### Enabling Remote Registry
# photo2

#### Turning on printer and Netdiscovery
# photo3

#### Screenshot of before VM configuration
# photo4 

#### Screenshot of after VM configuration
# photo5

#### Scan after installing older version of firefox onto VM
# photo6

#### Firefox Vulnerabilities
# photo7

#### Firefox Remediation recommendations from Nessus
# photo8

#### Firefox remediation in action: Updating Windows and deleting firefox
# photo9

#### Nessus scan after Windows updates and Firefox deletion
# photo10

#### Nessus remediation suggestions for last scan, if needed
# photo11

* * *

<button onclick="history.back()">Go Back</button>
