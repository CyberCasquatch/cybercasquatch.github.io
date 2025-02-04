---
layout: post
title:  "Understanding log sources & investigating with Splunk: Detecting Attacker Behavior With Splunk Based On TTPs"
date: 2025-02-4
author: C. Casquatch
comments: false
tag: ['spl', 'splunk', 'htb']
---

> In the ever-evolving world of cybersecurity, proficient threat detection is crucial. This necessitates a thorough understanding of the myriad tactics, techniques, and procedures (TTPs) utilized by potential adversaries, along with a deep insight into our own network systems and their typical behaviors. Effective threat detection often revolves around identifying patterns that either match known malicious behaviors or diverge significantly from expected norms.

In crafting detection-related SPL (Search Processing Language) searches in Splunk, we utilise two main approaches:

1\. Known Adversary TTPs
------------------------

The first approach is grounded in known adversary TTPs, leveraging our extensive knowledge of specific threats and attack vectors. This strategy is akin to playing a game of "spot the known." If an entity behaves in a way that we recognize as characteristic of a particular threat, it draws our attention.

2\. Anomaly Detection
---------------------

The second approach, while still informed by an understanding of attacker TTPs, leans heavily on statistical analysis and anomaly detection to identify abnormal behavior within the sea of normal activity. This strategy is more of a game of "spot the unusual." Here, we're not just relying on pre-existing knowledge of specific threats. Instead, we make extensive use of mathematical and statistical techniques to highlight anomalies, working on the premise that malicious activity will often manifest as an aberration from the norm.

Each methodology offers unique advantages and, when used in tandem, they create a robust detection mechanism. They allow us to identify known threats while also surfacing potential unknown risks. However, it is crucial to understand our data and environment, carefully tuning queries and thresholds to avoid false positives.

Now, let's dive deeper into these two approaches.

Crafting SPL Searches Based on Known TTPs
-----------------------------------------

The first approach focuses on recognizing patterns indicative of specific threats or attack vectors. Below are some examples:

### Example 1: Detection of Reconnaissance Activities Using Native Windows Binaries

Attackers often use native Windows binaries (such as net.exe) to gather insights into the target environment, identify potential privilege escalation opportunities, and perform lateral movement. Sysmon Event ID 1 can assist in identifying such behavior.

index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 Image=\*\\\\ipconfig.exe OR Image=\*\\\\net.exe OR Image=\*\\\\whoami.exe OR Image=\*\\\\netstat.exe OR Image=\*\\\\nbtstat.exe OR Image=\*\\\\hostname.exe OR Image=\*\\\\tasklist.exe | stats count by Image,CommandLine | sort - count
        

### Example 2: Detection of Requesting Malicious Payloads/Tools Hosted on Reputable Domains

Attackers frequently exploit reputable domains like githubusercontent.com for hosting payloads, which may bypass whitelisting filters. Sysmon Event ID 22 can assist in identifying such behavior.

index="main" sourcetype="WinEventLog:Sysmon" EventCode=22  QueryName="\*github\*" | stats count by Image, QueryName
        

### Example 3: Detection of PsExec Usage

PsExec is often used by attackers for lateral movement and remote execution. Sysmon Event IDs 13, 11, 17, and 18 can be used to detect its usage in an environment.

**Case 1: Leveraging Sysmon Event ID 13**

index="main" sourcetype="WinEventLog:Sysmon" EventCode=13 Image="C:\\\\Windows\\\\system32\\\\services.exe" TargetObject="HKLM\\\\System\\\\CurrentControlSet\\\\Services\\\\\*\\\\ImagePath" | rex field=Details "(?\[^\\\\\\\]+)$" | eval reg\_file\_name = lower(reg\_file\_name), file\_name = if(isnull(file\_name),reg\_file\_name,lower(file\_name)) | stats values(Image) AS Image, values(Details) AS RegistryDetails, values(\_time) AS EventTimes, count by file\_name, ComputerName
        

**Case 2: Leveraging Sysmon Event ID 11**

index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image=System | stats count by TargetFilename
        

**Case 3: Leveraging Sysmon Event ID 18**

index="main" sourcetype="WinEventLog:Sysmon" EventCode=18 Image=System | stats count by PipeName
        

### Example 4: Detection of Archive Files Used for Transferring Tools or Data Exfiltration

Attackers may use zip, rar, or 7z files for transferring tools or exfiltrating data. Here's a search to identify such activities.

index="main" EventCode=11 (TargetFilename="\*.zip" OR TargetFilename="\*.rar" OR TargetFilename="\*.7z") | stats count by ComputerName, User, TargetFilename | sort - count
        

### Example 5: Detection of Using PowerShell or MS Edge for Downloading Payloads/Tools

PowerShell or MS Edge are often exploited by attackers to download malicious payloads or tools.

index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image="\*powershell.exe\*" | stats count by Image, TargetFilename | sort + count
        

index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image="\*msedge.exe" TargetFilename=\*"Zone.Identifier" | stats count by TargetFilename | sort + count
        

### Example 6: Detection of Execution From Suspicious Locations

This search identifies process creation in a user's Downloads folder.

index="main" EventCode=1 | regex Image="C:\\\\\\\\Users\\\\\\\\.\*\\\\\\\\Downloads\\\\\\\\.\*" | stats count by Image
        

### Example 7: Detection of Executables or DLLs Created Outside the Windows Directory

This query identifies the creation of executables or DLLs outside the Windows directory.

index="main" EventCode=11 (TargetFilename="\*.exe" OR TargetFilename="\*.dll") TargetFilename!="\*\\\\windows\\\\\*" | stats count by User, TargetFilename | sort + count
        

### Example 8: Detection of Misspelled Legitimate Binaries

This search looks for misspelled binaries used to evade detection, such as variations of PSEXESVC.exe.

index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 (CommandLine="\*psexe\*.exe" NOT (CommandLine="\*PSEXESVC.exe" OR CommandLine="\*PsExec64.exe")) OR (ParentCommandLine="\*psexe\*.exe" NOT (ParentCommandLine="\*PSEXESVC.exe" OR CommandLine="\*PsExec64.exe")) OR (ParentImage="\*psexe\*.exe" NOT (ParentImage="\*PSEXESVC.exe" OR ParentImage="\*PsExec64.exe")) OR (Image="\*psexe\*.exe" NOT (Image="\*PSEXESVC.exe" OR Image="\*PsExec64.exe")) | table Image, CommandLine, ParentImage, ParentCommandLine
        

### Example 9: Detection of Non-Standard Port Communications

Attackers may utilize non-standard ports for communication or data transfer. This search identifies such behavior.

index="main" EventCode=3 NOT (DestinationPort=80 OR DestinationPort=443 OR DestinationPort=22 OR DestinationPort=21) | stats count by SourceIp, DestinationIp, DestinationPort | sort - count
        

By now, it's clear that a comprehensive understanding of attacker TTPs can significantly improve the speed and accuracy of threat detection. However, it's essential to remember that attackers are constantly evolving, and new, unknown techniques will always emerge. Relying solely on TTP-based searches is insufficient; anomaly detection and continuous monitoring remain key to robust cybersecurity.


### Questions

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through SPL searches against all data the password utilized during the PsExec activity. Enter it as your answer.
ANSWER:


<button onclick="history.back()">Go Back</button>
