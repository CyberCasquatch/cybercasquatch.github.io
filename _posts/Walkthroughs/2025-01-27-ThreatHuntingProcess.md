---
layout: post
title:  "Intro to threat hunting and hunting with elastic: Threat Hunting Process"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['threat hunting', 'elastic', 'htb']
---


> Key Phases and Steps


=============================================

1\. Setting the Stage:
----------------------

*   Plan and prepare: Understand the threat landscape, business requirements, and threat intelligence.
*   Ensure the environment is ready for hunting: Enable logging across systems and set up tools (SIEM, EDR, IDS).
*   Keep updated on emerging threats and threat actor profiles.
*   Example: Research Emotet's tactics and analyze critical systems that may be targeted.

2\. Formulating Hypotheses:
---------------------------

*   Create testable predictions based on threat intel, alerts, or intuition.
*   Identify potential attack vectors or signs of compromise.
*   Example: Hypothesis: Emotet is spreading through phishing emails with malicious Word docs.

3\. Designing the Hunt:
-----------------------

*   Determine data sources to examine: logs, network traffic, endpoint telemetry, etc.
*   Create queries, filters, and correlation rules to identify indicators of compromise (IoCs).
*   Develop custom scripts or use threat hunting tools to assist.
*   Example: Use email logs and network traffic to identify suspicious patterns linked to Emotet.

4\. Data Gathering and Examination:
-----------------------------------

*   Collect data: logs, network traffic, endpoint data.
*   Analyze data using the methods and tools chosen during the design phase.
*   Refine hypotheses and approach based on findings.
*   Example: Review logs for unusual email attachments or network traffic patterns.

<button onclick="history.back()">Go Back</button>
