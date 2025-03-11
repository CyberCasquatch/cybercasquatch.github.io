---
layout: post
title:  "Windows Event Logs and Finding Evil: Skills Assessment"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['windows', 'event logs', 'htb']
---

> Skills assessment for Windows Event Logs and Finidng Evil

RDP to [Target IP] using the provided credentials, examine the logs located in the C:\Logs\* directories, and answer the questions below.

CyberCasquatch@htb[/htb]$ xfreerdp /u:Administrator /p:'HTB_@cad3my_lab_W1n10_r00t!@0' /v:[Target IP] /dynamic-resolution

### Questions

#### By examining the logs located in the "C:\Logs\DLLHijack" directory, determine the process responsible for executing a DLL hijacking attack. Enter the process name as your answer. Answer format: _.exe
ANSWER:

#### By examining the logs located in the "C:\Logs\PowershellExec" directory, determine the process that executed unmanaged PowerShell code. Enter the process name as your answer. Answer format: _.exe
ANSWER:

#### By examining the logs located in the "C:\Logs\PowershellExec" directory, determine the process that injected into the process that executed unmanaged PowerShell code. Enter the process name as your answer. Answer format: _.exe
ANSWER:

#### By examining the logs located in the "C:\Logs\Dump" directory, determine the process that performed an LSASS dump. Enter the process name as your answer. Answer format: _.exe
ANSWER:

#### By examining the logs located in the "C:\Logs\Dump" directory, determine if an ill-intended login took place after the LSASS dump. Answer format: Yes or No
ANSWER:


#### By examining the logs located in the "C:\Logs\StrangePPID" directory, determine a process that was used to temporarily execute code based on a strange parent-child relationship. Enter the process name as your answer. Answer format: _.exe
ANSWER:


<button onclick="history.back()">Go Back</button>
