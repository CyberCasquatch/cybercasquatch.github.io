---
layout: post
title:  "SIEM Visualisation Example 3: Successful RDP Logon Related to Service Accounts"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

Walkthrough: SIEM Visualization Example 3 – Successful RDP Logon Related to Service Accounts
============================================================================================

Monitoring successful RDP (Remote Desktop Protocol) logons by service accounts is crucial for detecting unauthorized activity or potential misuse of privileged accounts. This guide outlines the steps to create and analyze a Kibana visualization for this use case.

1\. Objective
-------------

The aim of this visualization is to:

*   Identify successful RDP logons by service accounts.
*   Highlight patterns of access to sensitive systems.
*   Detect potentially unauthorized or unusual access activity.

\[Insert screenshot: Overview or example mockup of the final visualization\]

2\. Data Preparation
--------------------

### Indexing Log Data

Confirm your SIEM ingests RDP logon events and stores fields such as:

*   `event.action` (e.g., "logon-success")
*   `logon.type` (e.g., "RDP")
*   `user.account_type` or other indicators for service accounts.

\[Insert screenshot: Sample log data showing successful RDP logon events\]

### Filtering Relevant Events

Use a query in Kibana to isolate successful RDP logons by service accounts. Example:

    event.action: "logon-success" AND logon.type: "RDP" AND user.account_type: "service"

Test the query to ensure it retrieves accurate data.

\[Insert screenshot: Query results showing successful RDP logons for service accounts\]

3\. Creating the Visualization
------------------------------

### Step 1: Navigate to the Visualize Tab

Open Kibana and go to the Visualize Library.

Choose a visualization type (e.g., Bar Chart, Line Chart, or Pie Chart).

\[Insert screenshot: Kibana Visualize tab with visualization options\]

### Step 2: Configure the Data Source

Select the appropriate index pattern containing your log data.

Apply the filtered query for successful RDP logons by service accounts.

\[Insert screenshot: Data source configuration with index pattern and query\]

### Step 3: Define the Metrics

Y-Axis: Count of successful RDP logons.

X-Axis: Time intervals (e.g., daily, weekly).

Optionally, split the data by:

*   Hostnames: To track which systems were accessed.
*   Usernames: To see which service accounts are logging in.

\[Insert screenshot: Configured Y-Axis and X-Axis\]

4\. Refining the Visualization
------------------------------

### Customize Labels and Appearance

Add meaningful titles (e.g., "Successful RDP Logons by Service Accounts").

Customize axis labels, legends, and chart colors for better clarity.

\[Insert screenshot: Finalized visualization with proper formatting\]

### Save the Visualization

Save your visualization to an appropriate dashboard for quick access during incident response.

\[Insert screenshot: Save confirmation dialog\]

5\. Analyzing the Results
-------------------------

### Detecting Anomalies

Look for unexpected patterns, such as:

*   A service account accessing a system it doesn’t usually access.
*   RDP logons occurring at unusual hours.

Investigate spikes in activity to determine if they are legitimate or indicative of unauthorized access.

\[Insert screenshot: Highlighted anomalies in RDP logon activity\]

### Example Questions to Investigate:

*   Are the service accounts logging into unauthorized systems?
*   Do the access times align with scheduled maintenance windows or are they suspiciously irregular?

6\. Practical Use Case
----------------------

### Scenario

A service account logs into an RDP session during non-business hours.

After investigation, the SOC team discovers the credentials were compromised and used by an attacker to access sensitive systems.

\[Insert screenshot: Example use case with anomalous RDP logon events\]

7\. Conclusion
--------------

This visualization is an essential tool for SOC teams to:

*   Monitor privileged account activity.
*   Detect and investigate unusual patterns in RDP logons.
*   Strengthen security policies around service account usage.

\[Insert screenshot: Final visualization displayed in Kibana\]

<button onclick="history.back()">Go Back</button>
