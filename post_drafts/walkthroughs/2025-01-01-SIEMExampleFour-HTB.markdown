DRAFT
<!--
---
layout: post
title:  "Security Monitoring & SIEM Fundamentals: SIEM Visualisation Example 4 - Users Added Or Removed From a Local Group (Within A Specific Timeframe)"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---
-->
> In this SIEM visualisation example, we aim to monitor user additions or removals from the local **"Administrators"** group. This visualisation focuses on identifying changes made to high-privilege groups within a specific timeframe, from 5 March 2023 to the present.


===================================================================================================================

Setting Up the Target System
----------------------------

Navigate to the bottom of this section and click on **Click here to spawn the target system!**

Access the system at `http://[Target IP]:5601`. Open the side navigation toggle and click on **"Dashboard"**.

### Using the Prebaked Dashboard

*   Locate the prebaked dashboard and click the **"pencil"/edit** icon.
*   Click **Create visualisation** to start building a new visualisation.

### Creating the Visualisation

1.  In the new window, configure the following:

*   **Filter:** Add filters for Event IDs `4732` (member added) and `4733` (member removed). Additionally, filter by the group name "Administrators" to focus on relevant events.
*   **Index Pattern:** Use `windows*` to include relevant data.
*   **Search Bar:** Verify fields like `user.name.keyword` to ensure correct data.
*   **Visualisation Type:** Select "Table" for this visualisation.

3.  In the **Rows** settings, add fields such as:

*   `winlog.event_data.MemberSid.keyword` to identify the user added or removed.
*   `group.name.keyword` to confirm the affected group is "Administrators".
*   `event.action.keyword` to indicate whether the user was added or removed.
*   `host.name.keyword` to identify the machine where the action occurred.

5.  In the **Metrics** settings, select "count" as the metric to display the number of additions or removals.
6.  Set the timeframe to start from **5 March 2023**, ensuring your visualisation includes only relevant events.

#### Screenshot Placeholder

Add screenshots for the filter, rows, and metrics configurations, as well as the applied timeframe filter.

### Finalising the Visualisation

Ensure your table includes the following columns:

*   The user added to or removed from the group.
*   The group affected (to confirm it is "Administrators").
*   The action performed (add or remove).
*   The machine where the action occurred.
*   The count of events within the specified timeframe.

### Saving the Visualisation

*   Click **Save and return** to add the visualisation to the dashboard.
*   Title the visualisation appropriately and save the dashboard.

By following these steps, you can effectively monitor user additions and removals from the "Administrators" group, enhancing security awareness and compliance monitoring.

### Questions:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Dashboard". Extend the visualization we created or the "User added or removed from a local group" visualization, if it is available, and enter the common date on which all returned events took place as your answer. Answer format: 20XX-0X-0X
ANSWER:

<button onclick="history.back()">Go Back</button>
