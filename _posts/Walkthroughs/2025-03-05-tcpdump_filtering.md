---
layout: post
title:  "NOTES: Tcpdump Packet Filtering: Intro to Network Traffic Analysis"
date: 2025-03-5
author: C. Casquatch
comments: false
tag: ['traffic analysis', 'networking', 'tcpdump', 'htb']
---

Tcpdump provides a robust and efficient way to parse the data included in our captures via packet filters. This guide will explore filtering and advanced syntax options to streamline network traffic analysis.

Filtering and Advanced Syntax Options
-------------------------------------

Utilising advanced filtering options enables us to trim down traffic printed to output or saved to a file. Reducing the captured data helps conserve storage and improves buffer processing efficiency.

### Helpful Tcpdump Filters

*   **host**: Filters traffic involving a designated host (bi-directional).
*   **src / dest**: Specifies source or destination hosts/ports.
*   **net**: Displays traffic involving a designated network.
*   **proto**: Filters by protocol (e.g., TCP, UDP, ICMP).
*   **port**: Filters traffic using a specific port (bi-directional).
*   **portrange**: Specifies a range of ports (e.g., 0-1024).
*   **less / greater**: Filters packets by size.
*   **and / &&**: Combines multiple filter conditions.
*   **or**: Matches either of two conditions.
*   **not**: Excludes specified traffic.

### Example Commands

**Host Filter:**

    sudo tcpdump -i eth0 host 172.16.146.2

Insert Screenshot Here

**Source/Destination Filter:**

    sudo tcpdump -i eth0 src host 172.16.146.2

Insert Screenshot Here

**Port Filter:**

    sudo tcpdump -i eth0 tcp port 443

Insert Screenshot Here

Pre-Capture Filters vs. Post-Capture Processing
-----------------------------------------------

Applying filters directly to a live capture discards non-matching packets, which is useful for troubleshooting but may remove valuable data. Applying filters to saved capture files allows for flexible post-analysis without modifying the original capture.

Interpreting Tcpdump Output
---------------------------

*   **\-S**: Displays absolute sequence numbers.
*   **\-v, -X, -e**: Increases output verbosity.
*   **\-c, -n, -s, -S, -q**: Modifies output data.
*   **\-A**: Displays only ASCII text.
*   **\-l**: Enables real-time output for use with other tools.

### Example: Piping a Capture to Grep

    sudo tcpdump -Ar http.cap -l | grep 'mailto:*'

Insert Screenshot Here

### Example: Filtering TCP SYN Flags

    tcpdump -i eth0 'tcp[13] & 2 != 0'

Insert Screenshot Here


<button onclick="history.back()">Go Back</button>
