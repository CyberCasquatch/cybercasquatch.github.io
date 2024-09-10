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
![1](/assets/images/NessusLab/1.PNG)

#### Enabling Remote Registry
![2](/assets/images/NessusLab/2.PNG)

#### Turning on printer and Netdiscovery
![3](/assets/images/NessusLab/3.PNG)

#### Screenshot of before VM configuration
![4](/assets/images/NessusLab/beforeconfiguringvm.PNG)

#### Screenshot of after VM configuration
![5](/assets/images/NessusLab/afterconfiguringvm.PNG)

#### Scan after installing older version of firefox onto VM
![6](/assets/images/NessusLab/installedoldfirefoxscan.PNG)

#### Firefox Vulnerabilities
![7](/assets/images/NessusLab/firefoxvulns.PNG)

#### Firefox Remediation recommendations from Nessus
![8](/assets/images/NessusLab/firefoxremediation.PNG)

#### Firefox remediation in action: Updating Windows and deleting firefox
![9](/assets/images/NessusLab/updatingwindows.PNG)

#### Nessus scan after Windows updates and Firefox deletion
![10](/assets/images/NessusLab/scanafterupdates.PNG)

#### Nessus remediation suggestions for last scan, if needed
![11](/assets/images/NessusLab/remediationsforlastscan.PNG)

* * *

<button onclick="history.back()">Go Back</button>
