---
layout: post
title:  "NOTES: ARP Spoofing & Abnormality Detection: Intermediate Network Traffic Analysis"
date: 2025-03-9
author: C. Casquatch
comments: false
tag: ['traffic analysis', 'networking', 'arp spoofing', 'htb']
---

**Related PCAP File(s):** ARP\_Spoof.pcapng

Understanding Address Resolution Protocol (ARP)
-----------------------------------------------

ARP plays a crucial role in networking, enabling devices to discover the MAC addresses of other hosts based on their IP addresses. However, attackers often exploit this protocol for malicious activities such as man-in-the-middle (MITM) and denial-of-service (DoS) attacks. Due to its susceptibility, ARP is one of the first protocols we scrutinise in traffic analysis.

### How ARP Works

1.  Host A needs to send data to Host B and must determine its MAC address.
2.  Host A first checks its ARP cache to see if it already has this information.
3.  If the MAC address isn't cached, Host A broadcasts an ARP request across the network.
4.  Host B responds with its MAC address.
5.  Host A stores the MAC-IP mapping in its ARP cache for future use.
6.  Changes in network configuration (e.g., new interfaces or expired IPs) may require ARP cache updates.

ARP Poisoning & Spoofing
------------------------

In a secure environment, controls should prevent ARP spoofing, but in reality, attacks still occur. Detecting ARP-based attacks requires analysing traffic patterns and identifying anomalies.

### How ARP Spoofing Works

1.  An attacker sends falsified ARP messages to a victim and the router.
2.  The victim believes the attacker's machine is the router.
3.  The router believes the attacker's machine is the victim.
4.  Traffic from both parties is misdirected to the attacker.
5.  The attacker can now intercept or modify network traffic.
6.  If traffic forwarding is enabled, the attack escalates to a MITM scenario.
7.  Further attacks may include DNS spoofing or SSL stripping.

### Preventative Measures

*   **Static ARP Entries:** Prevents ARP cache manipulation but requires careful management.
*   **Switch & Router Port Security:** Restricts network access to authorised devices.

Capturing ARP Traffic with TCPDump
----------------------------------

Tools such as `tcpdump` and Wireshark are useful for capturing and analysing network traffic.

    sudo apt install tcpdump -y

    sudo tcpdump -i eth0 -w capture.pcapng

Detecting ARP Spoofing
----------------------

Using Wireshark, we can apply filters to identify abnormal ARP activity.

Open the capture file:

    wireshark ARP_Spoof.pcapng

### Key Wireshark Filters

*   `arp.opcode == 1` - Displays ARP requests.
*   `arp.opcode == 2` - Displays ARP replies.
*   `arp.duplicate-address-detected && arp.opcode == 2` - Identifies duplicate MAC addresses.

### Checking for Duplicate MAC Addresses

    arp -a | grep 50:eb:f6:ec:0e:7f

    arp -a | grep 08:00:27:53:0c:ba

If an IP address is mapped to multiple MAC addresses, ARP spoofing is likely occurring.

### Tracing Original IP Addresses

We can determine if an attacker has changed their IP address by using:

    (arp.opcode) && ((eth.src == 08:00:27:53:0c:ba) || (eth.dst == 08:00:27:53:0c:ba))

If a MAC address was previously associated with a different IP, it suggests an attack.

### Examining Suspicious Network Traffic

To inspect communications from specific MAC addresses, use:

    eth.addr == 50:eb:f6:ec:0e:7f or eth.addr == 08:00:27:53:0c:ba

Unstable TCP connections or mirrored transmissions could indicate a MITM attack.

### QUESTION

#### Inspect the ARP_Poison.pcapng file, part of this module's resources, and submit the total count of ARP requests (opcode 1) that originated from the address 08:00:27:53:0c:ba as your answer.
ANSWER:


<button onclick="history.back()">Go Back</button>
