---
layout: post
title:  "Understanding log sources & Investigating with Splunk: Intrusion detection with Splunk (Real world scenario)"
date: 2025-02-4
author: C. Casquatch
comments: false
tag: ['spl', 'splunk', 'htb']
---

> The goal is to explore how to detect potentially malicious activity, understand Sysmon Event IDs, and create robust alerts that can be effective in live environments. This walkthrough is based on a module from Hack The Box, and will not only serve as my personal reference, but also assist others in navigating through similar challenges.

Overview of Sysmon Event ID Definitions
---------------------------------------

Before we dive into the analysis, it’s important to understand the Sysmon Event IDs that are crucial for detecting malicious activity. Below are some of the primary Event IDs we’ll be using:

*   **Event ID 1: Process Creation** - Provides detailed information on process creation, including process ID, parent process ID, command line, and hashes.
*   **Event ID 10: Process Access** - Tracks the access to processes (such as reading or writing) and includes information about the source and target processes.
*   **Event ID 11: File Create** - Tracks the creation of files, providing information such as file paths and hashes.
*   **Event ID 13: Registry Value Set** - Monitors registry modifications, including which values were set, by which process, and the registry key.
*   **Event ID 12: Registry Object Access** - Monitors registry key access and includes details on the object being accessed and the accessing process.
*   **Event ID 7: Image Load** - Tracks the loading of executable images and includes path information.

These Event IDs are essential in identifying unusual or potentially malicious activities, which we'll leverage in this blog post to demonstrate intrusion detection methods.

Investigating the Logs
----------------------

### Step 1: Sorting and Identifying Frequent Activity

We prefer sorting by event count to make the data more comprehensible. Frequent activities are often considered "normal" in an environment, and are harder to identify as malicious when buried among a large number of events. To start, we’ll examine strange process accesses to **lsass.exe**, which is commonly targeted by attackers for credential dumping.

One noticeable process accessing **lsass.exe** is **notepad.exe**, which raises suspicion due to its rarity in such contexts. Another is **rundll32.exe**, which is infamously used by threat actors. Let’s begin by investigating these processes in Splunk.

#### Splunk Query:

index="main" EventCode=10 lsass SourceImage="C:\\\\Windows\\\\System32\\\\notepad.exe"
    

This query will search for instances of **notepad.exe** accessing **lsass.exe**. Although the data we have is limited, Sysmon suggests that this could be related to credential dumping. To understand how the attack occurred, we can use the **call stack** to trace the sequence of events and understand which processes triggered the malicious behavior.

### Step 2: Investigating the Call Stack

The **call stack** in Sysmon logs can provide valuable insight into the activity. A common pattern to look out for is the **UNKNOWN** segment in the call stack, which indicates shellcode execution. When processes access memory regions that don't map to identifiable files, it's a strong indication of potentially malicious behavior.

In this case, we observe that Sysmon has flagged this behavior, but it’s not always obvious at first glance. We can filter and search for these types of activities using the **call trace** field to better identify malicious activity.

#### Splunk Query:

index="main" CallTrace="\*UNKNOWN\*" | stats count by EventCode
    

This query identifies any activities where **UNKNOWN** segments appear in the **call trace**. It helps us narrow down the event codes related to suspicious activity, providing us with more context to build alerts.

### Step 3: Creating Alerts Based on UNKNOWN Call Traces

At this stage, we want to create alerts based on suspicious processes attempting to access memory regions that don’t map back to disk. However, false positives are common, especially with Just-In-Time (JIT) compiled processes like **.NET** applications. To reduce noise, we’ll filter out known non-malicious processes like JITs and avoid unnecessary alerts.

#### Splunk Query:

index="main" CallTrace="\*UNKNOWN\*" | where SourceImage!=TargetImage | stats count by SourceImage
    

This query helps us exclude instances where a process accesses itself, which is not generally a concern. After filtering, we can further refine our results.

#### Filtering Out False Positives:

To increase the alert's fidelity, we can filter out certain known patterns that may trigger false positives, such as **Microsoft.NET** processes, **ni.dll**, and **clr.dll** in the **call trace**.

#### Splunk Query:

index="main" CallTrace="\*UNKNOWN\*" SourceImage!="\*Microsoft.NET\*" CallTrace!=\*ni.dll\* CallTrace!=\*clr.dll\* | where SourceImage!=TargetImage | stats count by SourceImage
    

### Step 4: Building a Robust Alert System

At this stage, we have a refined alert system capable of identifying unusual process behavior based on **UNKNOWN** call traces. To further improve this alert, we can reintroduce some of the removed fields like **TargetImage** and **CallTrace** to ensure we don’t overlook any potential threats.

#### Final Splunk Query:

index="main" CallTrace="\*UNKNOWN\*" SourceImage!="\*Microsoft.NET\*" CallTrace!=\*ni.dll\* CallTrace!=\*clr.dll\* CallTrace!=\*wow64\* SourceImage!="C:\\\\Windows\\\\Explorer.EXE" | where SourceImage!=TargetImage | stats count by SourceImage, TargetImage, CallTrace
    

### Real-World Considerations

In a real-world scenario, the amount of data could be much larger, and additional filtering or more nuanced queries would be needed. Attackers could also attempt to bypass this alert by using different DLLs or modifying their call traces. It’s important to continuously adapt the alert system to account for new attack techniques.

In this blog post, we’ve explored how to use **Splunk** to detect malicious activities based on **Sysmon logs**. By leveraging **Event IDs** such as **Event ID 10** (Process Access) and focusing on **UNKNOWN** call traces, we’ve been able to build an alert system to detect suspicious behavior. We’ve also discussed ways to reduce false positives by excluding known benign processes and improving the alert system’s fidelity.

While this lab environment was relatively simplified, real-world environments often present much more complex datasets. However, the principles discussed here—such as filtering based on **call traces**, understanding **Sysmon Event IDs**, and crafting high-fidelity alerts—remain applicable as you scale up to larger environments.

### Questions

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through an SPL search against all data the other process that dumped lsass. Enter its name as your answer. Answer format: _.exe
ANSWER:

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through SPL searches against all data the method through which the other process dumped lsass. Enter the misused DLL's name as your answer. Answer format: _.dll
ANSWER:

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through an SPL search against all data any suspicious loads of clr.dll that could indicate a C# injection/execute-assembly attack. Then, again through SPL searches, find if any of the suspicious processes that were returned in the first place were used to temporarily execute code. Enter its name as your answer. Answer format: _.exe
ANSWER:

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through SPL searches against all data the two IP addresses of the C2 callback server. Answer format: 10.0.0.1XX and 10.0.0.XX
ANSWER:

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through SPL searches against all data the port that one of the two C2 callback server IPs used to connect to one of the compromised machines. Enter it as your answer.
ANSWER:



<button onclick="history.back()">Go Back</button>
