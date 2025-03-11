---
layout: post
title:  "Windows Event Logs and Finding Evil: Get-WinEvent"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['windows', 'event logs', 'htb']
---

> Comprehensive Guide to Filtering and Analyzing Windows Event Logs with PowerShell

===================================================================================

When investigating suspicious activity or performing threat analysis, analyzing Windows Event Logs can provide valuable insights into system behavior. PowerShell’s `Get-WinEvent` cmdlet is an invaluable tool for extracting, filtering, and analyzing event logs from both live systems and exported `.evtx` files. In this guide, we will walk through different techniques for querying event logs, filtering for specific criteria, and extracting relevant data to help with incident response and security investigations.

1\. Retrieving Events from `.evtx` Files
----------------------------------------

In many scenarios, event logs are exported to `.evtx` files from other systems for offline analysis or archiving. PowerShell’s `Get-WinEvent` cmdlet makes it easy to retrieve events from these files. You can specify the path to the `.evtx` file and then filter or analyze the events in the file just as you would with live logs.

### Example: Retrieve Events from an Exported `.evtx` File

    PS C:\Users\Administrator> Get-WinEvent -Path 'C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Execution\exec_sysmon_1_lolbin_pcalua.evtx' -MaxEvents 5 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize

This command loads events from the specified file and displays key details like:

*   **TimeCreated**: When the event occurred
*   **ID**: The Event ID
*   **ProviderName**: The event provider (e.g., Sysmon)
*   **LevelDisplayName**: The event severity level
*   **Message**: The event’s description

You can adjust `MaxEvents` to control the number of events retrieved.

2\. Filtering Events with `FilterHashtable`
-------------------------------------------

The `-FilterHashtable` parameter is a powerful tool that allows you to apply specific conditions to filter events directly within PowerShell. This method is useful when you want to filter by event log name, event ID, or date ranges.

### Example 1: Filter by Event ID and Log Name

    PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1,3} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize

This will return events related to process creation and network connections, which are often useful for detecting suspicious activity.

### Example 2: Filter by Date Range

You can also filter events by date range. For instance, to retrieve events between May 28, 2023, and June 2, 2023, use the following commands:

    PS C:\Users\Administrator> $startDate = (Get-Date -Year 2023 -Month 5 -Day 28).Date
    PS C:\Users\Administrator> $endDate = (Get-Date -Year 2023 -Month 6 -Day 3).Date
    PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1,3; StartTime=$startDate; EndTime=$endDate} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize

Note that `StartTime` is inclusive, while `EndTime` is exclusive, meaning June 3rd is not included in the results.

3\. Filtering Events with `FilterHashtable` & XML
-------------------------------------------------

Another powerful filtering method involves parsing event logs in XML format. This is particularly useful when analyzing specific event details that are nested in XML elements. In this case, we use Sysmon Event ID 3 (network connection) to detect network activity related to a particular IP address.

### Example: Filter by IP Address Using XML

    PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=3} |
    ForEach-Object {
        $xml = [xml]$_.ToXml()
        $eventData = $xml.Event.EventData.Data
        New-Object PSObject -Property @{
            SourceIP = $eventData | Where-Object {$_.Name -eq "SourceIp"} | Select-Object -ExpandProperty '#text'
            DestinationIP = $eventData | Where-Object {$_.Name -eq "DestinationIp"} | Select-Object -ExpandProperty '#text'
            ProcessGuid = $eventData | Where-Object {$_.Name -eq "ProcessGuid"} | Select-Object -ExpandProperty '#text'
            ProcessId = $eventData | Where-Object {$_.Name -eq "ProcessId"} | Select-Object -ExpandProperty '#text'
        }
    }  | Where-Object {$_.DestinationIP -eq "52.113.194.132"}

This script retrieves Sysmon Event ID 3 (Network Connection) logs, extracts specific event data (source and destination IP, process GUID, and process ID), and filters for connections to a particular suspicious IP address.

4\. Filtering Events with `FilterXPath`
---------------------------------------

Using XPath queries provides even more flexibility for filtering event logs. The `-FilterXPath` parameter allows you to write custom XPath queries to retrieve logs based on specific patterns or conditions.

### Example: Detecting Suspicious DLL Loads with XPath

    PS C:\Users\Administrator> $Query = @"
        
            
                *[System[(EventID=7)]] and *[EventData[Data='mscoree.dll']] or *[EventData[Data='clr.dll']]
                
            
        
    "@
    PS C:\Users\Administrator> Get-WinEvent -FilterXml $Query | ForEach-Object {Write-Host $_.Message `n}

This XPath query searches for Event ID 7 (Image Load) events where `mscoree.dll` or `clr.dll` are loaded. These DLLs are often associated with malicious behavior, and detecting their presence in unusual processes can help identify threats.

### Example: Identifying Sysinternals Tool Installation

    PS C:\Users\Administrator> Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -FilterXPath "*[EventData[Data[@Name='Image']='C:\Windows\System32\reg.exe']] and *[EventData[Data[@Name='CommandLine']='`"C:\Windows\system32\reg.exe`" ADD HKCU\Software\Sysinternals /v EulaAccepted /t REG_DWORD /d 1 /f']]"

This query looks for registry modifications related to the installation of Sysinternals tools, specifically detecting the acceptance of the End User License Agreement (EULA).

5\. Filtering Events Based on Property Values
---------------------------------------------

In some cases, you may need to filter events based on specific property values, such as identifying processes that use a particular command line argument or checking for unusual command-line patterns.

### Example: Filtering Process Creation Events by Command Line

    PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1} | Where-Object {$_.Properties[21].Value -like "*-enc*"} | Format-List

This command filters Sysmon Event ID 1 (Process Create) events where the parent command line contains the string `-enc`, which is commonly used in PowerShell to obfuscate malicious commands.

Conclusion
----------

By leveraging the power of PowerShell’s `Get-WinEvent` cmdlet, filtering event logs using a variety of methods like `FilterHashtable`, `FilterXml`, `FilterXPath`, and direct property value checks, we can effectively analyze Windows event logs for security events and potential threats. Understanding these techniques and applying them in your own investigations will help improve your ability to detect and respond to incidents more efficiently.

We hope this guide has provided you with the knowledge to start querying and analyzing event logs with PowerShell. Remember, event logs are a goldmine for security analysts—knowing how to extract meaningful information is key to detecting malicious activity in your environment.

### Questions

#### Utilize the Get-WinEvent cmdlet to traverse all event logs located within the "C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Lateral Movement" directory and determine when the \\*\PRINT share was added. Enter the time of the identified event in the format HH:MM:SS as your answer.
ANSWER:



<button onclick="history.back()">Go Back</button>
