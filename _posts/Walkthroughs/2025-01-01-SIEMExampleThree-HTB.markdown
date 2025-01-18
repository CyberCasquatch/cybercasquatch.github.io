---
layout: post
title:  "SIEM Visualisation Example 3: Successful RDP Logon Related to Service Accounts"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> Monitoring successful RDP (Remote Desktop Protocol) logons by service accounts is crucial for detecting unauthorised activity or potential misuse of privileged accounts. This guide outlines the steps to create and analyse a Kibana visualization for this use case.

1\. Objective
-------------

The aim of this visualisation is to:

*   Identify successful RDP logons by service accounts.
*   Highlight patterns of access to sensitive systems.
*   Detect potentially unauthorised or unusual access activity.

\[Insert screenshot: Overview or example mockup of the final visualisation\]

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

3\. Creating the Visualisation
------------------------------

### Step 1: Navigate to the Visualise Tab

Open Kibana and go to the Visualise Library.

Choose a visualisation type (e.g., Bar Chart, Line Chart, or Pie Chart).

\[Insert screenshot: Kibana Visualise tab with visualization options\]

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

4\. Refining the Visualisation
------------------------------

### Customise Labels and Appearance

Add meaningful titles (e.g., "Successful RDP Logons by Service Accounts").

Customise axis labels, legends, and chart colors for better clarity.

\[Insert screenshot: Finalised visualisation with proper formatting\]

### Save the Visualisation

Save your visualisation to an appropriate dashboard for quick access during incident response.

\[Insert screenshot: Save confirmation dialog\]

5\. Analyzing the Results
-------------------------

### Detecting Anomalies

Look for unexpected patterns, such as:

*   A service account accessing a system it doesn’t usually access.
*   RDP logons occurring at unusual hours.

Investigate spikes in activity to determine if they are legitimate or indicative of unauthorised access.

\[Insert screenshot: Highlighted anomalies in RDP logon activity\]

### Example Questions to Investigate:

*   Are the service accounts logging into unauthorised systems?
*   Do the access times align with scheduled maintenance windows or are they suspiciously irregular?

6\. Practical Use Case
----------------------

### Scenario

A service account logs into an RDP session during non-business hours.

After investigation, the SOC team discovers the credentials were compromised and used by an attacker to access sensitive systems.

\[Insert screenshot: Example use case with anomalous RDP logon events\]

7\. Conclusion
--------------

This visualisation is an essential tool for SOC teams to:

*   Monitor privileged account activity.
*   Detect and investigate unusual patterns in RDP logons.
*   Strengthen security policies around service account usage.

\[Insert screenshot: Final visualisation displayed in Kibana\]

<button onclick="history.back()">Go Back</button>
