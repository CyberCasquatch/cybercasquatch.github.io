---
layout: post
title:  "Windows Event Logs and Finding Evil: Windows Event Logs "
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['windows', 'event logs', 'htb']
---

> By leveraging the Windows Event Logs and understanding the significance of different event IDs, system administrators and cybersecurity professionals can monitor, detect, and investigate potential threats in their environment. Regular monitoring of these event logs and correlating them with other system logs enables early threat detection and proactive incident response. Moreover, crafting custom XML queries and implementing centralized logging solutions can optimize the analysis of event logs, making the detection of suspicious behavior more efficient and precise.

Windows Event Logs are an essential feature of the Windows Operating System, storing critical logs from various system components such as the system itself, applications, services, ETW (Event Tracing for Windows) providers, and other subsystems. These logs are invaluable for tracking application errors, security events, system diagnostics, and much more. As cybersecurity professionals, we heavily rely on these logs for detecting intrusions, monitoring system health, and performing forensic investigations.

Event logs are categorized into several types to organize events based on their origin or function. Some key event log categories include:

*   **Application Logs:** Logs related to the execution of applications and services.
*   **System Logs:** Logs related to system-level events, such as hardware or OS issues.
*   **Security Logs:** Logs for tracking security-related events such as login attempts, permission changes, and audit failures.

Event logs can be accessed in various ways. The most common methods are via the **Event Viewer** application or programmatically through APIs like the Windows Event Log API. For security-focused analysis, administrative access to Event Viewer is required.

The default Windows event logs consist of several categories:

*   **Application:** Captures events related to installed applications.
*   **Security:** Logs security-related events such as successful/failed logins, resource access, etc.
*   **Setup:** Logs related to system setup and installation activities.
*   **System:** Captures events related to system components and services.
*   **Forwarded Events:** Logs event data forwarded from other machines, useful for centralized logging.

In addition to these logs, the Windows Event Viewer allows users to open and display previously saved `.evtx` files, which can be accessed from the "Saved Logs" section.

The Anatomy of an Event Log
---------------------------

Every entry in the Windows Event Log is known as an "Event." These events provide detailed information about the system's state at the time of the log entry and contain several key components:

*   **Log Name:** The name of the event log (e.g., Application, System, Security).
*   **Source:** The software or system component that generated the event.
*   **Event ID:** A unique identifier for the event, used for categorizing and searching.
*   **Task Category:** Indicates the specific type or function of the event.
*   **Level:** Specifies the severity of the event (e.g., Information, Warning, Error, Critical).
*   **Keywords:** Flags used to categorize events for further search and filtering (e.g., "Audit Success," "Audit Failure").
*   **User:** The user account that triggered the event.
*   **OpCode:** Provides the operation being reported by the event.
*   **Logged:** The date and time when the event was recorded.
*   **Computer:** The name of the computer where the event occurred.
*   **XML Data:** A machine-readable format containing all the above data, along with additional event details.

Understanding and filtering events using these components allows security professionals to pinpoint specific issues more efficiently. For example, the **Event ID** provides a direct way to reference detailed event information, often available on Microsoft's official documentation.

Security Logs and Event ID 4624
-------------------------------

A crucial event for security monitoring is **Event ID 4624**, which indicates a successful logon. According to [Microsoft's documentation](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624), this event represents the creation of a new logon session and contains important details such as the **Logon ID** and **Logon Type**, which help in correlating activities and tracking potentially suspicious logon patterns.

*   **Logon ID:** A unique identifier used to track all related logon events.
*   **Logon Type:** Specifies the logon type (e.g., interactive logon, network logon, service logon).

By correlating **Event ID 4624** with other events using the **Logon ID**, we can identify patterns in the logins across systems, helping pinpoint unauthorized access or unusual activity.

Leveraging Custom XML Queries
-----------------------------

One powerful feature of Windows Event Viewer is the ability to write custom XML queries, which can filter events based on specific criteria. For instance, if we wanted to filter events associated with a particular **Logon ID**, we could use the "Filter Current Log" -> "XML" -> "Edit Query Manually" option. This capability helps narrow down the events for more precise analysis.

Example custom XML query:

            <QueryList>
                <Query Id="0" Path="Security">
                    <Select Path="Security">
                        \*\[System\[(EventID=4624)\]\]
                        and
                        \*\[EventData\[Data\[@Name='LogonID'\] and (Data='0x3E7')\]\]
                    </Select>
                </Query>
            </QueryList>
        

This query focuses on **Event ID 4624**, filtering by **Logon ID**. Custom queries can significantly improve the efficiency of analyzing large volumes of event logs, especially in security investigations.

Common Windows Event IDs and Their Use Cases
--------------------------------------------

The following is a list of some important **Windows Event IDs**, grouped by category. These events are particularly useful for identifying potential security threats and system anomalies.

### Windows System Logs

*   **Event ID 1074:** System Shutdown/Restart – Indicates when and why the system was shut down or restarted.
*   **Event ID 6005:** The Event Log Service was started – Marks when the Event Log Service started, often associated with system boot.
*   **Event ID 6006:** The Event Log Service was stopped – Typically seen during system shutdown.
*   **Event ID 6013:** Windows uptime – Shows the uptime of the system in seconds.
*   **Event ID 7040:** Service status change – Records when a service startup type changes.

### Windows Security Logs

*   **Event ID 1102:** The audit log was cleared – A red flag for attempts to remove evidence of intrusions.
*   **Event ID 1116:** Antivirus malware detection – Logs when malware is detected by antivirus software.
*   **Event ID 1118:** Antivirus remediation activity has started – Indicates the start of malware removal or quarantining.
*   **Event ID 1119:** Antivirus remediation activity has succeeded – Indicates successful removal of malware.
*   **Event ID 1120:** Antivirus remediation activity has failed – Signifies failure to remediate malware.
*   **Event ID 4624:** Successful Logon – Logs successful user logon events.
*   **Event ID 4625:** Failed Logon – Logs failed logon attempts, useful for detecting brute-force attacks.
*   **Event ID 4648:** A logon was attempted using explicit credentials – Indicates use of explicit credentials for application logon.
*   **Event ID 4656:** A handle to an object was requested – Triggered when an object handle (file, registry key, etc.) is requested.
*   **Event ID 4672:** Special Privileges Assigned to a New Logon – Logs when a user logs on with elevated privileges.
*   **Event ID 4698:** A scheduled task was created – Logs the creation of a scheduled task, potentially indicating persistence mechanisms.
*   **Event ID 4700:** A scheduled task was enabled – Indicates enabling of a scheduled task, potentially a sign of suspicious activity.
*   **Event ID 4701:** A scheduled task was disabled – Indicates the disabling of a scheduled task, potentially used for evasion.
*   **Event ID 4702:** A scheduled task was updated – Logs any updates to scheduled tasks, which could suggest malicious modifications.
*   **Event ID 4719:** System audit policy was changed – Indicates changes to the audit policy, which could signal an attempt to cover up malicious activities.
*   **Event ID 4738:** A user account was changed – Logs changes to user accounts, such as privilege modifications or password changes.
*   **Event ID 4771:** Kerberos pre-authentication failed – Logs failed Kerberos authentication attempts.
*   **Event ID 4776:** The domain controller attempted to validate the credentials for an account – Logs failures in domain controller credential validation.
*   **Event ID 5001:** Antivirus real-time protection configuration has changed – Logs modifications to antivirus real-time protection settings.
*   **Event ID 5140:** A network share object was accessed – Logs access attempts to network shares.
*   **Event ID 5142:** A network share object was added – Logs when a new network share is created.
*   **Event ID 5145:** A network share object was checked to see whether client can be granted desired access – Logs attempts to access network shares.
*   **Event ID 5157:** The Windows Filtering Platform has blocked a connection – Indicates blocked network connections, useful for detecting malicious traffic.
*   **Event ID 7045:** A service was installed in the system – Logs installation of a new service, potentially indicating malware installation.

### Questions

####Analyze the event with ID 4624, that took place on 8/3/2022 at 10:23:25. Conduct a similar investigation as outlined in this section and provide the name of the executable responsible for the modification of the auditing settings as your answer. Answer format: T_W_____.exe
ANSWER

####Build an XML query to determine if the previously mentioned executable modified the auditing settings of C:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dll. Enter the time of the identified event in the format HH:MM:SS as your answer.
ANSWER

<button onclick="history.back()">Go Back</button>

