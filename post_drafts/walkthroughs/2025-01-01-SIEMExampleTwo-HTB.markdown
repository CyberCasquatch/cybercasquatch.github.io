---
layout: post
title:  "Security Monitoring & SIEM Fundamentals Module: SIEM Visualisation Example 2 - Failed Logon Attempts (Disabled Users)"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> In this SIEM visualisation example, we aim to monitor failed login attempts against disabled users. These logins are never successful, even with correct credentials, and the failure is logged with a SubStatus value of `0xC0000072`.

Setting Up the Target System
----------------------------

Navigate to the bottom of this section and click on **Click here to spawn the target system!**

Access the system at `http://[Target IP]:5601`. Open the side navigation toggle and click on **"Dashboard"**.

### Using the Prebaked Dashboard

*   Locate the prebaked dashboard and click the **"pencil"/edit** icon.
*   Click **Create visualisation** to start building a new visualisation.

### Creating the Visualisation

1.  In the new window, configure the following:

*   **Filter:** Add a filter for Event ID `4625` and SubStatus `0xC0000072` (indicating failed logins for disabled users).
*   **Index Pattern:** Use `windows*` to include relevant data.
*   **Search Bar:** Verify fields like `user.name.keyword` to ensure correct data.
*   **Visualisation Type:** Select "Table" for this visualisation.

3.  In the **Rows** settings, add fields such as:

*   `user.name.keyword` for the disabled user.
*   `host.hostname.keyword` for the machine name.

5.  In the **Metrics** settings, select "count" as the metric to display the number of failed logins.

#### Screenshot Placeholder

Add screenshots for the filter, rows, and metrics configurations.

### Finalizing the Visualisation

Ensure your table includes three columns:

*   The disabled user whose credentials generated the event.
*   The machine where the logon attempt occurred.
*   The count of failed logon attempts within the specified time frame.

### Saving the Visualisation

*   Click **Save and return** to add the visualisation to the dashboard.
*   Title the visualisation appropriately and save the dashboard.

With these steps, you can effectively monitor failed logins against disabled users, ensuring better visibility into potential security issues.

### Questions:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Either create a new visualization or edit the "Failed logon attempts [Disabled user]" visualization, if it is available, so that it includes failed logon attempt data related to disabled users including the logon type. What is the logon type in the returned document?
ANSWER:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Either create a new visualization or edit the "Failed logon attempts [Admin users only]" visualization, if it is available, so that it includes failed logon attempt data where the username field contains the keyword "admin" anywhere within it. What should you specify after user.name: in the KQL query?
ANSWER:

<button onclick="history.back()">Go Back</button>
