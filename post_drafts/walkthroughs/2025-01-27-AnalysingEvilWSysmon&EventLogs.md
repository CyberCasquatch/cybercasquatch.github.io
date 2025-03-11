---
layout: post
title:  "Windows Event Logs and Finding Evil: Analysing Evil with Sysmon and Event Logs "
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['windows', 'event logs', 'sysmon', 'htb']
---

> Sysmon Basics: A Guide to Monitoring and Detection for Cybersecurity Learners


=============================================================================

When investigating malicious events, certain Windows Event IDs often serve as critical indicators of compromise (IOCs). For instance, Event ID 4624 helps track new logon events, while Event ID 4688 records process creation—both crucial for spotting suspicious activities. To enhance your monitoring, you can incorporate **Sysmon** (System Monitor), a powerful tool for capturing detailed system activity beyond the standard Security Event logs.

What is Sysmon?
---------------

**Sysmon** is a Windows system service and device driver that stays active across reboots to log detailed system activity to the Windows Event Log. It provides insights into:

*   Process creation
*   Network connections
*   File creation time changes
*   And more

Unlike standard logs, Sysmon captures deeper details, making it invaluable for forensic analysis and threat detection. Its logging capabilities are organized into event IDs. For example:

*   **Event ID 1**: Process creation
*   **Event ID 3**: Network connections

Configuring Sysmon
------------------

### Installation:

1.  Download Sysmon from Microsoft's official [Sysinternals page](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon).
2.  Open a command prompt as Administrator and run the installation command:

    sysmon.exe -i -accepteula -h md5,sha256,imphash -l -n

### Custom Configuration:

To refine what Sysmon logs, use an XML configuration file. Popular templates include:

*   [SwiftOnSecurity's Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config): Comprehensive setup
*   [Olaf Hartong's Sysmon Modular](https://github.com/olafhartong/sysmon-modular): Modular approach

Apply a custom configuration like this:

    sysmon.exe -c filename.xml

Detection Examples
------------------

### Example 1: **DLL Hijacking**

**Scenario:** A DLL hijacking attack involves a malicious DLL loaded by a legitimate executable, exploiting the system’s search order for DLLs. To detect this using Sysmon:

*   **Relevant Sysmon Event ID:** **Event ID 7** (Module Load)

#### Steps to Detect DLL Hijacking:

1.  Download the [Sysmon configuration file](https://github.com/SwiftOnSecurity/sysmon-config).
2.  Modify it to log all module loads (change `exclude` to `include` for DLLs).
3.  Apply the configuration:

    sysmon.exe -c sysmonconfig-export.xml

4.  Open Event Viewer: Navigate to `Applications and Services Logs > Microsoft > Windows > Sysmon`. Filter for Event ID 7 to view loaded modules.

### Example 2: **Unmanaged PowerShell or C# Injection**

**Scenario:** C# code is executed through the Common Language Runtime (CLR). Observing unusual CLR module loads (e.g., `clr.dll`, `clrjit.dll`) in non-C# processes could indicate malicious activity.

*   **Relevant Sysmon Event ID:** **Event ID 7** (Module Load)

#### Steps:

1.  Use [Process Hacker](https://processhacker.sourceforge.io/) to identify managed processes (highlighted in green).
2.  Monitor suspicious transitions of unmanaged processes (e.g., `spoolsv.exe`) to a managed state.

### Example 3: **Credential Dumping (e.g., Mimikatz)**

**Scenario:** Credential-dumping tools like Mimikatz target LSASS (Local Security Authority Subsystem Service) to extract credentials.

*   **Relevant Sysmon Event ID:** **Event ID 10** (Process Access)

#### Steps to Detect:

1.  Monitor access attempts to LSASS by non-standard processes.
2.  IOCs:

*   Random executables attempting to access LSASS.
*   Use of `SeDebugPrivilege` (often requested by Mimikatz).

Practical Exercises
-------------------

#### Setting Up:

1.  Spawn the target system in the lab environment.
2.  RDP into the system using provided credentials.
3.  Install Sysmon and configure it using the steps above.

#### Tasks:

*   Replicate attacks like DLL hijacking, PowerShell injection, and credential dumping.
*   Analyze logs to identify key indicators and understand the telemetry provided by Sysmon.


### Questions:

#### Replicate the DLL hijacking attack described in this section and provide the SHA256 hash of the malicious WININET.dll as your answer. "C:\Tools\Sysmon" and "C:\Tools\Reflective DLLInjection" on the spawned target contain everything you need.
ANSWER:

#### Replicate the Unmanaged PowerShell attack described in this section and provide the SHA256 hash of clrjit.dll that spoolsv.exe will load as your answer. "C:\Tools\Sysmon" and "C:\Tools\PSInject" on the spawned target contain everything you need.
ANSWER:

#### Replicate the Credential Dumping attack described in this section and provide the NTLM hash of the Administrator user as your answer. "C:\Tools\Sysmon" and "C:\Tools\Mimikatz" on the spawned target contain everything you need.
ANSWER:




####


<button onclick="history.back()">Go Back</button>
