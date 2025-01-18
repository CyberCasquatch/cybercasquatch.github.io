---
layout: post
title:  "SIEM Visualisation Example 3: Successful RDP Logon Related to Service Accounts"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

> In this SIEM visualization example, we aim to monitor successful RDP logons specifically related to service accounts. These accounts, which often have high privileges, should not be used for RDP logons. IT Operations confirms that all service accounts in the environment start with `svc-`.

Setting Up the Target System
----------------------------

Navigate to the bottom of this section and click on **Click here to spawn the target system!**

Access the system at `http://[Target IP]:5601`. Open the side navigation toggle and click on **"Dashboard"**.

### Using the Prebaked Dashboard

*   Locate the prebaked dashboard and click the **"pencil"/edit** icon.
*   Click **Create visualization** to start building a new visualization.

### Creating the Visualization

1.  In the new window, configure the following:

*   **Filter:** Add a filter for Event ID `4624` and Logon Type `RemoteInteractive` (`winlog.logon.type` field).
*   **Index Pattern:** Use `windows*` to include relevant data.
*   **Search Bar:** Verify fields like `user.name.keyword` to ensure correct data.
*   **Visualization Type:** Select "Table" for this visualization.

3.  In the **Rows** settings, add fields such as:

*   `user.name.keyword` for the service account.
*   `host.hostname.keyword` for the machine where the logon occurred.
*   `related.ip.keyword` for the IP of the initiating machine.

5.  In the **Metrics** settings, select "count" as the metric to display the number of successful logons.
6.  Apply a KQL query to filter service accounts starting with `svc-`:
    
    user.name: svc-\*
    

#### Screenshot Placeholder

Add screenshots for the filter, rows, and metrics configurations, as well as the applied KQL query.

### Finalizing the Visualization

Ensure your table includes four columns:

*   The service account whose credentials generated the logon event.
*   The machine where the logon occurred.
*   The IP of the machine that initiated the logon attempt.
*   The count of logon events within the specified time frame.

### Saving the Visualization

*   Click **Save and return** to add the visualization to the dashboard.
*   Title the visualization appropriately and save the dashboard.

With these steps, you can effectively monitor successful RDP logons related to service accounts, ensuring better visibility into potential security issues.

###QUESTIONS

####Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Browse the visualization we created or the "RDP logon for service account" visualization, if it is available, and enter the IP of the machine that initiated the successful RDP logon using service account credentials as your answer.
ANSWER:


<button onclick="history.back()">Go Back</button>
