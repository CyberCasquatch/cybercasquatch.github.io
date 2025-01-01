---
layout: post
title:  "SIEM Visualisation Example 4: Users Added Or Removed From a Local Group (Within A Specific Timeframe)"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'elastic']
---

  SIEM Visualisation Example 4 Walkthrough

Walkthrough: SIEM Visualisation Example 4 - Users Added or Removed from a Local Group (Within a Specific Timeframe)
===================================================================================================================

Introduction
------------

In this walkthrough, we will cover how to create a SIEM (Security Information and Event Management) visualisation for detecting when users are added or removed from a local group within a specified timeframe. This can help in identifying potential unauthorised changes to user groups on a system.

Step 1: Setting Up the Environment
----------------------------------

Before diving into the SIEM visualisation setup, ensure your environment is properly configured with the necessary logs and data sources.

*   Ensure you have access to the event logs from Windows machines.
*   Set up your SIEM tool (e.g., Splunk, ELK Stack) to collect these logs.
*   Enable auditing on local group changes (e.g., through Group Policy settings).

### Screenshot 1: Configuration of the SIEM tool to collect Windows Event Logs

Step 2: Identifying the Relevant Windows Event Logs
---------------------------------------------------

The key to detecting changes to local groups is identifying the correct event IDs that indicate users being added or removed from groups.

*   Look for Event ID 4728 (A user was added to a security-enabled global group) for adding users to groups.
*   Look for Event ID 4729 (A user was removed from a security-enabled global group) for removing users.
*   Adjust your SIEM query to filter for these event IDs.

### Screenshot 2: Filtering Windows Event Logs for Event IDs 4728 and 4729

Step 3: Building the Query in SIEM
----------------------------------

Now that we have identified the relevant logs, the next step is to create a SIEM query to track users added or removed from local groups within a specified timeframe.

*   Open your SIEM tool and navigate to the query creation section.
*   Construct the query to capture events 4728 and 4729.
*   Set the timeframe to filter events within a specific range, for example, the past 24 hours.

### Screenshot 3: Building the SIEM query to capture events 4728 and 4729

Step 4: Creating the Visualisation
----------------------------------

With the query in place, we can now create the visualisation that will alert us to changes in local group membership.

*   Choose a visualisation type that best suits your needs (e.g., table, bar chart, or time series).
*   Map the relevant fields such as **User**, **Group**, **Action (Added/Removed)**, and **Timestamp**.
*   Set the necessary parameters for the visualisation (e.g., time range, alerting thresholds).

### Screenshot 4: Creating the SIEM visualisation based on the query results

Step 5: Testing the Visualisation
---------------------------------

Now that we have set up the visualisation, it’s time to test it by simulating some changes in local group membership.

*   Add or remove a user from a local group manually on a test machine.
*   Verify that the SIEM tool captures the event and reflects the changes in the visualisation.

### Screenshot 5: Example of visualisation showing a user added/removed from a local group

Step 6: Interpreting the Results
--------------------------------

Once the test events are captured, analyse the results and check for any abnormal activity.

*   Review the visualisation for any unexpected or unauthorised changes to local groups.
*   Investigate any anomalies and determine if they pose a security risk.

### Screenshot 6: Example of an anomalous event detected in the visualisation

Conclusion
----------

By following these steps, we have successfully created a SIEM visualisation for detecting users being added or removed from local groups within a specific timeframe. This can be a valuable tool for monitoring and auditing user group membership, helping to identify potential security threats or unauthorised actions.

<button onclick="history.back()">Go Back</button>
