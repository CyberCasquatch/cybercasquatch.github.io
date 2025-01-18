---
layout: post
title:  "SIEM Visualisation Example 1: Failed Logon Attempts (All Users)"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> Tracking failed logon attempts is a critical use case for any SIEM solution. This guide walks you through creating a visualization in Kibana to monitor failed logon attempts for all users.

## 1\. Objective

The goal of this visualization is to:

*   Identify patterns or spikes in failed logon attempts.
*   Highlight potential unauthorized access attempts.
*   Enable SOC teams to respond quickly to suspicious activity.

**\[Insert screenshot: Brief overview or mockup of the final visualization\]**

## 2\. Data Preparation

### Indexing Log Data

*   Ensure your log data is ingested into Elasticsearch.
*   Confirm that fields such as `event.action` (or equivalent) and `user.name` are correctly indexed.

**\[Insert screenshot: Sample indexed log data showing failed logon events\]**

### Filtering Relevant Events

Use a query in Kibana to filter failed logon events. For example:

event.action: "logon-failed"

Verify the query returns accurate results.

**\[Insert screenshot: Kibana query results showing failed logon events\]**

## 3\. Creating the Visualization

### Step 1: Navigate to the Visualize Tab

*   Open Kibana and click on the Visualize Library.
*   Select a suitable visualization type (e.g., Line Chart, Bar Chart, or Pie Chart).

**\[Insert screenshot: Kibana Visualize tab with available visualization options\]**

### Step 2: Configure the Data Source

*   Choose the appropriate index pattern for your log data.
*   Apply the filtered query for failed logon attempts (`event.action: "logon-failed"`).

**\[Insert screenshot: Visualization configuration panel showing the index pattern and query\]**

### Step 3: Define the Metrics

*   **Y-Axis:** Count of failed logon events.
*   **X-Axis:** Time interval (e.g., per hour, day).

**\[Insert screenshot: Configured Y-Axis and X-Axis for the visualization\]**

### Step 4: Add Splits (Optional)

*   Split the data by `user.name` to show failed attempts for individual users.

**\[Insert screenshot: Split configuration showing user-based breakdowns\]**

## 4\. Refining the Visualization

### Customize Labels and Appearance

*   Add meaningful titles (e.g., "Failed Logon Attempts by User").
*   Format axes and legend for better readability.

**\[Insert screenshot: Completed visualization with customized labels and styling\]**

### Save the Visualization

*   Save the visualization to the appropriate folder or dashboard.

**\[Insert screenshot: Save confirmation dialog in Kibana\]**

## 5\. Analyzing the Results

### Detecting Anomalies

*   Look for unexpected spikes or consistent patterns of failed attempts.
*   Correlate with other log data (e.g., successful logons or geolocation) to validate findings.

**\[Insert screenshot: Highlighted anomalies in the visualization\]**

### Example Questions to Ask:

*   Are there repeated failed attempts for a specific user?
*   Are the failures occurring from unexpected IP addresses or geolocations?

## 6\. Practical Use Case

### Scenario

A sudden spike in failed logon attempts is detected.

SOC teams investigate the source of these attempts and identify a brute-force attack.

**\[Insert screenshot: Example use case showing a suspicious spike in logon failures\]**

## 7\. Conclusion

Visualizing failed logon attempts in Kibana is a powerful way to proactively identify security threats. By leveraging SIEM tools, you can:

*   Spot anomalies in real time.
*   Harden access controls based on observed attack patterns.
*   Enhance your organization’s overall security posture.

**\[Insert screenshot: Final visualization as seen in Kibana\]**


<button onclick="history.back()">Go Back</button>
