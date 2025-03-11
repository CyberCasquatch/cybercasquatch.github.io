---
layout: post
title:  "Understanding log sources & investigating with Splunk: Detecting Attacker Behavior With Splunk Based On Analytics"
date: 2025-02-4
author: C. Casquatch
comments: false
tag: ['spl', 'splunk', 'htb']
---

> In this blog post, I will be walking through my experience with the **Splunk** module on **Hack The Box**. I'll share detailed reference notes, step-by-step instructions, and key Splunk queries that can be helpful when investigating suspicious processes using **Sysmon logs** in Windows environments. The goal is to help others answer related questions effectively and assist with practical usage of Splunk for intrusion detection.

Understanding Suspicious Processes
----------------------------------

Upon analysing the results from Sysmon event logs, I found a few suspicious processes that were flagged in earlier steps. However, not all of them were immediately visible. It's crucial to remember that this kind of behavior can also be exhibited by legitimate software in many cases. Thus, context and further investigation are key to confirming if any detected activity is truly malicious.

Detection of Transactions: Same Process Created More Than Once
--------------------------------------------------------------

A potential sign of malicious behavior is when the same process (Image) is created multiple times on the same machine (ComputerName). This could indicate abnormal or suspicious activity depending on the nature of the involved processes. In order to monitor such behavior, I created the following **Splunk Search Processing Language (SPL)** query:

    
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
    | transaction ComputerName, Image
    | where mvcount(ProcessGuid) > 1
    | stats count by Image, ParentImage
                

Here’s a breakdown of each part of the query:

*   **index="main" sourcetype="WinEventLog:Sysmon" EventCode=1**: This retrieves all Sysmon process creation events (EventCode=1) from the main index. Sysmon event code 1 indicates a process creation event and includes details like the process that was started, its command line arguments, the initiating user, and its parent process.
*   **| transaction ComputerName, Image**: This _transaction_ command groups related events together based on shared values in the _ComputerName_ and _Image_ fields. This helps us identify all process creation events linked to the same program on a specific machine.
*   **| where mvcount(ProcessGuid) > 1**: The _mvcount(ProcessGuid) > 1_ condition filters the results to show transactions where the same process (Image) has been initiated more than once on the same computer. This typically indicates that the same program was launched multiple times in a short period.
*   **| stats count by Image, ParentImage**: The _stats_ command counts how many times these events occurred, grouped by _Image_ (the process name) and _ParentImage_ (its parent process). This gives you a summary of the program and its parent process.

Focusing on Rundll32.exe and Svchost.exe
----------------------------------------

One of the key findings in my analysis was the pair of processes `rundll32.exe` and `svchost.exe`, which appeared with the highest count in the transaction results. To investigate this further, I refined the query to focus specifically on these two processes:

    
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1
    | transaction ComputerName, Image
    | where mvcount(ProcessGuid) > 1
    | search Image="C:\\Windows\\System32\\rundll32.exe" ParentImage="C:\\Windows\\System32\\svchost.exe"
    | table CommandLine, ParentCommandLine
                

This query searches for `rundll32.exe` being launched by `svchost.exe`, which is often used by attackers for malicious payloads or execution. After running the query, I identified suspicious command-line arguments associated with these processes, pointing to potential indicators of compromise (IoC).

Detecting Deviations from Normal Behavior
-----------------------------------------

By establishing a profile of "normal" behavior and implementing a statistical model to detect deviations, I could have more quickly detected the compromise of my environment. This is especially true when paired with a solid understanding of **attackers’ tactics, techniques, and procedures (TTPs)**, which would help identify unusual patterns.

However, it’s important to note that relying solely on statistical models and these queries is not enough to definitively confirm an intrusion. False positives are always a possibility, and context is key in investigating any anomalies. For example, legitimate processes might trigger these queries under certain conditions, so further analysis, such as checking logs and correlating with other data sources, is essential for accurate threat detection.

Conclusion
----------

In summary, this exercise in using Splunk with Sysmon logs for process detection highlighted a few key insights:

*   **Repeated process creation** could be an indication of abnormal activity.
*   Monitoring the relationship between `rundll32.exe` and `svchost.exe` is important for identifying malicious payloads.
*   A baseline of normal behavior, combined with statistical models, can help identify deviations more quickly.

This blog post serves as a personal reference for my learning journey through the Hack The Box **Splunk** module, as well as a guide for others who may be facing similar questions. Always remember, the most critical part of incident detection is understanding context and continuously refining your queries and models.

### Questions

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through an analytics-driven SPL search against all data the source process images that are creating an unusually high number of threads in other processes. Enter the outlier process name as your answer where the number of injected threads is greater than two standard deviations above the average. Answer format: _.exe
ANSWER:



<button onclick="history.back()">Go Back</button>
