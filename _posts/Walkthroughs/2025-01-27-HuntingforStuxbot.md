---
layout: post
title:  "Intro to threat hunting & hunting with Elastic; Hunting for Stuxbot"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['threat hunting', 'elastic', 'htb']
---

> A Comprehensive Walkthrough

================================================

This blog post serves as a detailed walkthrough of the "Hunting for Stuxbot" investigation, based on insights from Windows Event Logs and Sysmon data. It's structured as a guide to help identify, trace, and analyse potential malicious activity. Let's dive in!

* * *

Part 1: Initial Observations
----------------------------

Our investigation begins with data indicating that a suspicious file, `invoice.one`, was downloaded by a user named Bob from `file.io`. This raised immediate concerns about potential malware, prompting us to investigate further.

### Choice of Approach

At this stage, we had two options:

*   Cross-referencing the data with a Threat Intelligence report to identify overlapping indicators.
*   Conducting an Incident Response (IR)-like investigation to trace the sequence of events post-download.

We opted for the second approach to fully understand the impact and subsequent activities.

### Query: Identifying the File's Access

Hypothetically, if `invoice.one` was accessed, it would have been opened using the OneNote application. We constructed the following query to verify this:

`event.code:1 AND process.command_line:*invoice.one*`

This query searches for Sysmon Event ID 1 (process creation) where the command line includes `invoice.one`. Our results confirmed that the file was accessed just six seconds after its download.

### Follow-up Query: Processes Spawned by OneNote

Once opened, `OneNote.exe` could potentially spawn other processes, either launching a browser to access a malicious link or executing a harmful attachment. To investigate, we used:

`event.code:1 AND process.parent.name:"ONENOTE.EXE"`

This query returned three results, of which two were relevant:

*   **OneNoteM.exe**: A legitimate OneNote component used for launching files.
*   **cmd.exe**: Executing `invoice.bat` from a temporary directory, strongly suggesting malicious intent.

* * *

Part 2: Tracing the Batch File
------------------------------

### Query: Child Processes of `invoice.bat`

Next, we investigated whether `invoice.bat` spawned additional processes:

`event.code:1 AND process.parent.command_line:*invoice.bat*`

This revealed a single result: the launch of PowerShell with suspicious arguments. The command aimed to download and execute a script from Pastebin, an open text-hosting platform.

### Analysis: PowerShell Activity

To understand what PowerShell did, we filtered logs using its process ID:

`process.pid:"9944" AND process.name:"powershell.exe"`

We observed the following activities:

*   File creation.
*   Network connections to a suspicious IP.
*   DNS queries for `ngrok.io`, indicating potential Command-and-Control (C2) traffic.

Additional context revealed that `ngrok` was likely used to obscure malicious communications.

### Query: Checking for Execution of `default.exe`

The investigation uncovered a dropped executable named `default.exe`. We searched for its execution using:

`process.name:"default.exe"`

The results confirmed its execution, revealing further activities:

*   DNS queries for `ngrok.io`.
*   Connections to a C2 IP address.
*   Uploads of `svchost.exe` and `SharpHound.exe`.

* * *

Part 3: Analysing Lateral Movement
----------------------------------

### SharpHound Execution

SharpHound is a tool used for Active Directory reconnaissance. To determine its usage, we queried:

`process.name:"SharpHound.exe"`

Results confirmed that it was executed twice, approximately two minutes apart, likely gathering information about the Active Directory environment.

### Tracking Compromise of `svc-sql1`

Evidence suggested that the attacker compromised the `svc-sql1` account. To investigate password attempts, we queried:

`(event.code:4624 OR event.code:4625) AND winlog.event_data.LogonType:3 AND source.ip:192.168.28.130`

This revealed failed logon attempts for the administrator account, followed by successful attempts for `svc-sql1`, indicating a brute-force attack.

* * *

Part 4: Final Observations
--------------------------

### Broad Search: Hash-Based Investigation

To identify other devices compromised by the same malware, we conducted a hash-based search:

`process.hash.sha256:018d37cbd3878258c29db3bc3f2988b6ae688843801b9abc28e6151141ab66d4`

The malware was found on `WS001` and `PKI`. Analysis revealed that the attacker leveraged PSExec for lateral movement, further compromising `svc-sql1`.

### Key Takeaways

*   Thorough log analysis is crucial for tracing the sequence of events in a breach.
*   Indicators of Compromise (IOCs) like hashes, IP addresses, and domains are invaluable for correlating activity across devices.
*   Advanced tools like SharpHound and PSExec highlight the attacker's sophistication and intent to escalate privileges.

* * *

### Questions

#### Navigate to http://[Target IP]:5601 and follow along as we hunt for Stuxbot. In the part where default.exe is under investigation, a VBS file is mentioned. Enter its full name as your answer, including the extension.
ANSWER:

####  Stuxbot uploaded and executed mimikatz. Provide the process arguments (what is after .\mimikatz.exe, ...) as your answer.
ANSWER:

#### Some PowerShell code has been loaded into memory that scans/targets network shares. Leverage the available PowerShell logs to identify from which popular hacking tool this code derives. Answer format (one word): P____V___
ANSWER:

<button onclick="history.back()">Go Back</button>





