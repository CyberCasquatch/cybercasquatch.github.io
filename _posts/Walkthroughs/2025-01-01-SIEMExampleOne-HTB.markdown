---
layout: post
title:  "SIEM Visualisation Example 1: Failed Logon Attempts (All Users)"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> Tracking failed logon attempts is a critical use case for any SIEM solution. This guide walks you through creating a visualisation/Dashboard.

### Steps to Create a Dashboard

*   Delete the existing "SOC-Alerts" dashboard if it exists.
*   Click on the **Create new dashboard** button to start creating your first dashboard.
*   Click on **Create visualisation** to begin building visualisations.

### Configuring a Visualisation

1.  Specify the date range as **last 15 years** using the time picker and click **Apply**.
2.  Use filters, e.g., to display failed logon attempts, filter for event ID `4625`.
3.  Select the data set by specifying the `windows*` index pattern.
4.  Search for specific fields, e.g., `user.name.keyword`, to ensure accurate data access.
5.  Choose the visualisation type, e.g., "Table," and configure rows and metrics.
6.  Add fields like `host.hostname.keyword` to display the hostname alongside counts of failed logon attempts.

#### Screenshot Placeholder

Add your screenshots here to showcase each step or configuration.

### Refining the Visualisation

Based on feedback from a SOC Manager, refinements include:

*   Clearer column names.
*   Adding **Logon Type** using the `winlog.logon.type.keyword` field.
*   Sorting the results for better readability.
*   Excluding irrelevant usernames, such as `DESKTOP-DPOESND`.
*   Filtering out computer accounts using a KQL query, e.g., `NOT user.name: *$ AND winlog.channel.keyword: Security`.

#### Screenshot Placeholder

Add screenshots showcasing refined visualisations and updated dashboards.

### Saving and Finalizing

Don't forget to:

*   Save your visualisation by clicking **Save and return**.
*   Title your visualisation for easy identification.
*   Save the entire dashboard.

By following these steps, you can create and refine meaningful dashboards and visualizations in the Elastic Stack to enhance security monitoring and data analysis.
**\[Insert screenshot: Final visualisation as seen in Kibana\]**

### Question:

####Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Browse the refined visualization we created or the "Failed logon attempts [All users]" visualization, if it is available, and enter the number of logins for the sql-svc1 account as your answer.
ANSWER:

<button onclick="history.back()">Go Back</button>
