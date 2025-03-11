---
layout: post
title:  "Windows Event Logs and Finding Evil: Event Tracing for Windows"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['windows', 'event logs', 'htb']
---

>  A Comprehensive Guide to Event Tracing for Windows (ETW)

========================================================

Event Tracing for Windows (ETW) is a powerful and versatile framework designed by Microsoft, enabling developers and security professionals to monitor, debug, and analyze system activities. This guide provides a deep dive into ETW, focusing on its architecture, practical applications, and specific providers that are invaluable for cybersecurity.

* * *

What is ETW?
------------

ETW is a high-performance tracing facility built into the Windows operating system. It provides visibility into the inner workings of the OS, applications, and kernel processes, offering critical insights into system behavior. With minimal impact on performance, ETW is widely used for performance monitoring, debugging, and security event analysis.

The architecture of ETW revolves around three core components:

*   **Providers**: Sources of events (e.g., Microsoft-Windows-Winlogon, Microsoft-Windows-Kernel-Process).
*   **Controllers**: Tools that configure and enable providers to generate events (e.g., `logman.exe`, Performance Monitor).
*   **Consumers**: Applications or scripts that process the generated event logs (e.g., Event Viewer, custom scripts).

* * *

Querying ETW Providers
----------------------

To query ETW providers, we can leverage command-line tools like `logman.exe`. For example, querying the Microsoft-Windows-Winlogon provider can be done as follows:

    C:\Tools> logman.exe query providers Microsoft-Windows-Winlogon

This yields a detailed list of available keywords, levels, and other metadata:

Value

Keyword

Description

0x0000000000010000

PerfInstrumentation

Performance instrumentation

0x0000400000000000

ms:Measures

Measures

0x8000000000000000

win:EventlogClassic

Classic event logging

Value

Level

Description

0x02

win:Error

Error events

0x03

win:Warning

Warning events

0x04

win:Informational

Informational events

* * *

GUI-Based Alternatives
----------------------

For those who prefer graphical interfaces, the Performance Monitor tool is an excellent resource. It allows users to:

*   Visualize active trace sessions.
*   Examine details of a specific trace by double-clicking.
*   Modify existing sessions by adding or removing providers.
*   Create new sessions under the "User Defined" category.

For in-depth exploration of ETW metadata, tools like [EtwExplorer](https://github.com/google/etwexplorer) offer a convenient way to browse and understand provider details.

* * *

Key ETW Providers for Security Monitoring
-----------------------------------------

ETW providers are essential for tracking specific activities within Windows. Here are some of the most useful ones for cybersecurity:

### Process and System Monitoring

*   **Microsoft-Windows-Kernel-Process**: Tracks process-related activity, useful for detecting anomalies like process injection or hollowing.
*   **Microsoft-Windows-Kernel-Registry**: Monitors registry changes, helpful for identifying persistence mechanisms or unauthorized modifications.
*   **Microsoft-Windows-Kernel-File**: Focuses on file operations, aiding in the detection of ransomware or exfiltration attempts.

### Network Activity Monitoring

*   **Microsoft-Windows-Kernel-Network**: Provides insights into network activity, including unauthorized connections and C2 communication.
*   **Microsoft-Windows-DNS-Client**: Tracks DNS requests, helping to identify DNS tunneling or other suspicious activities.
*   **Microsoft-Windows-SMBClient/SMBServer**: Monitors SMB traffic to detect lateral movement or unusual file sharing patterns.

### PowerShell and Scripting

*   **Microsoft-Windows-PowerShell**: Logs PowerShell activity, including script block execution and potential exploitation attempts.

### Malware Detection and Integrity Checks

*   **Microsoft-Windows-CodeIntegrity**: Tracks code and driver integrity, aiding in identifying unsigned or malicious code.
*   **Microsoft-Antimalware-Service**: Monitors antimalware operations, useful for detecting disabled services or evasion attempts.

### Remote Access and Authentication

*   **WinRM (Windows Remote Management)**: Reveals unauthorized or suspicious remote management activity.
*   **Microsoft-Windows-TerminalServices-LocalSessionManager**: Tracks local and remote Terminal Services sessions.

### Specialized Monitoring

*   **Microsoft-Windows-DotNETRuntime**: Focuses on .NET runtime events, helping to detect exploitation or malicious .NET assemblies.
*   **Microsoft-Windows-VPN-Client**: Tracks VPN client events to identify unauthorized connections.

* * *

Restricted Providers
--------------------

Some ETW providers are restricted to privileged processes due to the sensitive nature of their telemetry. An example is the **Microsoft-Windows-Threat-Intelligence** provider, often leveraged in Digital Forensics and Incident Response (DFIR) operations. Accessing these providers typically requires processes to run with Protected Process Light (PPL) privileges.

### Accessing Restricted Providers

Microsoft enforces stringent requirements for privileged processes, including:

*   Implementing an Early Launch Anti-Malware (ELAM) driver.
*   Undergoing a Microsoft certification process for a special Authenticode signature.

Although these restrictions ensure security, they also pose challenges for researchers and incident responders. Workarounds, while technically possible, should only be explored within legal and ethical boundaries.

* * *

Why ETW Over Sysmon?
--------------------

While Sysmon is a popular tool for monitoring and analyzing system events, it has limitations in capturing certain low-level or specialized events. ETW fills this gap by offering:

*   Real-time telemetry for kernel-level operations.
*   Enhanced granularity for advanced attack detection.
*   Flexibility to monitor a wider range of activities using various providers.

For example, ETW can capture subtle behaviors like advanced persistence mechanisms or encrypted C2 communications that may evade Sysmon’s scope.

* * *

Practical Use Cases of ETW
--------------------------

*   **Detecting Malware Behavior**:
    *   Use the Microsoft-Windows-Kernel-Process provider to identify unusual child process creation or injection attempts.
    *   Leverage the PowerShell provider to detect obfuscated or malicious script execution.
*   **Analyzing Network Attacks**:
    *   Monitor the DNS Client provider for tunneling or C2 activity.
    *   Use the SMBServer provider to trace lateral movement in a compromised network.
*   **Monitoring Integrity and Compliance**:
    *   Employ the Code Integrity provider to ensure that only signed drivers and code are loaded.
    *   Use the Antimalware Service provider to verify that security mechanisms remain operational.

* * *

Conclusion
----------

Event Tracing for Windows (ETW) is a cornerstone of Windows security monitoring. Its ability to capture granular events in real time makes it indispensable for incident response, threat detection, and forensic investigations. By understanding and leveraging key ETW providers, security professionals can gain unparalleled insights into system activities and detect sophisticated threats.

For those starting with ETW, tools like `logman`, Performance Monitor, and EtwExplorer provide an excellent foundation. Whether you’re analyzing process injections, detecting DNS tunneling, or ensuring system integrity, ETW’s flexibility and power make it a critical component of any security toolkit.
