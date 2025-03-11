---
layout: post
title:  "Intro to threat hunting & hunting with Elastic; Skills Assessment"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['threat hunting', 'elastic', 'htb']
---

> Hunting for Stuxbot: Round 2

============================

In this second round of Stuxbot analysis, new insights reveal its evolving tactics. The latest version of Stuxbot now leverages the C:\\Users\\Public directory for deploying additional tools and uses registry run keys to maintain persistence. It also utilises PowerShell Remoting to move laterally within the network and target domain controllers.

The investigation employs the Elastic stack as a SIEM solution, pulling data from various sources including Windows audit logs, Sysmon logs, PowerShell logs, and Zeek network logs.

Key Tasks:
----------

*   **Hunt 1:** Query for "Lateral Tool Transfer" to C:\\Users\\Public and find the user involved in the transfer.
*   **Hunt 2:** Query for "Registry Run Keys / Startup Folder" to identify persistence actions.
*   **Hunt 3:** Query for "PowerShell Remoting" to track lateral movement towards DC1.

### Questions

#### Enter your answer for Hunt 1.
ANSWER:

#### Enter your answer for Hunt 2.
ANSWER:

#### Enter your answer for Hunt 3.
ANSWER:



<button onclick="history.back()">Go Back</button>
