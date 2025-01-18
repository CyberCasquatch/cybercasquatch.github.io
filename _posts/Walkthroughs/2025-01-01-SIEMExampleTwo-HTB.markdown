---
layout: post
title:  "SIEM Visualisation Example 2: Failed Logon Attempts (Disabled Users)"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> Monitoring failed logon attempts for disabled users is critical for detecting potential misuse of inactive accounts, a common target for malicious actors. This guide outlines the process of creating a visualization in Kibana to track these events.

1\. Objective
-------------

The objective of this visualization is to:

*   Identify unauthorized access attempts on disabled user accounts.
*   Highlight patterns or spikes that may indicate malicious activity.
*   Assist SOC teams in investigating and mitigating risks tied to inactive accounts.

\[Insert screenshot: Overview or mockup of the final visualization\]

2\. Data Preparation
--------------------

### Indexing Log Data

Verify that your log data includes fields such as `event.action` (e.g., "logon-failed") and `user.account_status` (e.g., "disabled").

Confirm that these fields are indexed and accessible in Kibana.

\[Insert screenshot: Sample log data showing failed logon attempts for disabled accounts\]

### Filtering Relevant Events

Use a Kibana query to isolate failed logon attempts where the user account is disabled. Example:

    event.action: "logon-failed" AND user.account_status: "disabled"

Test the query to ensure it returns accurate results.

\[Insert screenshot: Query results showing failed logon events for disabled users\]

3\. Creating the Visualization
------------------------------

### Step 1: Navigate to the Visualize Tab

Go to Kibana’s Visualize Library.

Choose a visualization type, such as Bar Chart, Pie Chart, or Line Chart.

\[Insert screenshot: Kibana Visualize tab showing visualization options\]

### Step 2: Configure the Data Source

Select the relevant index pattern containing the log data.

Apply the query for failed logon attempts by disabled users.

\[Insert screenshot: Data source configuration with index pattern and query\]

### Step 3: Define the Metrics

Y-Axis: Count of failed logon events.

X-Axis: Time intervals (e.g., per hour, day, or week).

\[Insert screenshot: Configured Y-Axis and X-Axis in the visualization\]

### Step 4: Add Splits (Optional)

Split the data by `user.name` or `user.account_status` to identify specific users or accounts with the most failed attempts.

\[Insert screenshot: Split configuration showing breakdown by user\]

4\. Refining the Visualization
------------------------------

### Customize Labels and Appearance

Add descriptive titles (e.g., "Failed Logon Attempts on Disabled User Accounts").

Adjust axis labels, legends, and colors for clarity.

\[Insert screenshot: Finalized visualization with proper formatting\]

### Save the Visualization

Save your work to a designated folder or dashboard for easy reference.

\[Insert screenshot: Save confirmation dialog\]

5\. Analyzing the Results
-------------------------

### Spotting Patterns

Look for unexpected spikes in failed logon attempts for disabled accounts.

Correlate these attempts with other data points, such as IP addresses or geolocations, for further analysis.

\[Insert screenshot: Highlighted spikes in failed logon attempts\]

### Example Investigation Questions:

*   Are there consistent attempts on specific disabled accounts?
*   Are these attempts originating from known or trusted sources?

6\. Practical Use Case
----------------------

### Scenario

A disabled account shows multiple failed logon attempts within a short timeframe.

Upon investigation, the SOC team discovers a brute-force attack targeting decommissioned employee accounts.

\[Insert screenshot: Example dashboard showing anomalous activity\]

7\. Conclusion
--------------

This visualization helps SOC teams proactively monitor failed logon attempts on disabled accounts, a frequent indicator of targeted attacks. By implementing this use case, organizations can:

*   Enhance account management policies.
*   Identify and mitigate potential security gaps in inactive accounts.
*   Strengthen overall incident response capabilities.

\[Insert screenshot: Final visualization displayed in Kibana\]

<button onclick="history.back()">Go Back</button>
