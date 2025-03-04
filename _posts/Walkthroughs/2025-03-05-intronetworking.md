---
layout: post
title:  "NOTES: Intro to Metwork Traffic Analysis"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['traffic analysis', 'networking', 'htb']
---


Network Traffic Analysis (NTA) refers to the process of scrutinising network activity to identify common ports and protocols, establish a normal traffic baseline, detect anomalies, and enhance overall network security. This practice assists security professionals in identifying threats early, responding effectively, and ensuring compliance with security standards.

Attackers frequently refine their tactics to evade detection, often using legitimate credentials and tools permitted within an organisation’s network. This makes threat detection and mitigation particularly challenging. NTA plays a crucial role in identifying these threats. Common applications include:

*   Real-time traffic analysis to identify emerging threats.
*   Establishing a baseline for typical network communications.
*   Detecting non-standard port usage, suspicious hosts, and protocol anomalies (e.g., HTTP errors, TCP issues).
*   Identifying malicious traffic, such as ransomware or exploit attempts.
*   Conducting forensic investigations and proactive threat hunting.

For instance, if a network experiences an unusual number of SYN packets on ports rarely used, this could indicate a port scanning attempt. Identifying and responding to such activities requires expertise in networking fundamentals and security analysis.

Required Skills and Knowledge
-----------------------------

Effective network traffic analysis necessitates both theoretical knowledge and practical experience. The following areas are key:

*   **TCP/IP Stack & OSI Model:** Understanding how network traffic interacts with host applications.
*   **Basic Networking Concepts:** Recognising different traffic types across network layers, including switching and routing principles.
*   **Common Ports and Protocols:** Familiarity with standard ports and their expected communication patterns.
*   **IP Packets and Sublayers:** Understanding TCP and UDP communication structures to better interpret network traffic.
*   **Protocol Transport Encapsulation:** Analysing how data is encapsulated at various layers to facilitate deeper inspection.

Tools and Equipment
-------------------

Various tools facilitate network traffic analysis, each offering unique capabilities for capturing, interpreting, and analysing traffic:

*   [tcpdump](https://www.tcpdump.org/) – A command-line tool using LibPcap to capture and analyse network packets.
*   TShark – A command-line counterpart to Wireshark for network packet analysis.
*   [Wireshark](https://www.wireshark.org/) – A graphical tool for capturing and dissecting network traffic.
*   NGrep – A network-focused version of grep, useful for pattern matching in network packets.
*   tcpick – A command-line tool specialising in tracking and reconstructing TCP streams.
*   Network Taps – Hardware devices used to duplicate network traffic for analysis.
*   SPAN Ports – Used to mirror traffic from networking devices for monitoring.
*   Elastic Stack – A collection of tools for aggregating, visualising, and searching network data.
*   SIEMs – Security Information and Event Management (SIEM) systems like Splunk assist with centralised traffic analysis and alerting.

Berkeley Packet Filter (BPF) Syntax
-----------------------------------

Many of the aforementioned tools use [Berkeley Packet Filter (BPF)](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter) syntax, which enables filtering and decoding network traffic. BPF provides a powerful way to extract relevant network data efficiently. More details can be found in the [IBM documentation](https://www.ibm.com/docs/en/aix/7.2?topic=protocols-berkeley-packet-filters).

Performing Network Traffic Analysis
-----------------------------------

Network traffic analysis can range from monitoring live traffic streams to capturing and processing data through a SIEM. To monitor traffic effectively, analysts must be positioned within the correct network segment. VLANs and switch configurations, such as SPAN ports and port mirroring, assist in obtaining a copy of network traffic for inspection.

### Network Traffic Analysis Workflow

1.  **Ingest Traffic:** Capture network traffic based on placement strategy, using filters to focus on relevant data.
2.  **Reduce Noise:** Filter out unnecessary traffic (e.g., broadcast and multicast) to simplify analysis.
3.  **Analyse and Explore:** Examine network activity, focusing on key indicators like encryption, access attempts, and host interactions.
4.  **Detect and Alert:** Identify errors or anomalies and determine whether they indicate benign activity or a security threat. Intrusion detection systems (IDS) and intrusion prevention systems (IPS) aid in this process.
5.  **Fix and Monitor:** Implement solutions and continue monitoring to ensure issues are resolved effectively.


Networking Refresher - layers 1-4
====================

This section serves as a quick refresher on networking and how some standard protocols function while performing traffic captures.

OSI / TCP-IP Models
-------------------

### Networking Models

Insert OSI/TCP-IP Model Diagram Here

The OSI model and the TCP-IP model are graphical representations of communication between networked computers.

### Model Traits Comparison

Trait

OSI - seven layers, strict flexibility, dependency is protocol independent & generic

TCP-IP - Four layers, flexible, depenency is based on common communication protocols

### PDU Example

Insert PDU Breakdown Image Here

A Protocol Data Unit (PDU) is a data packet containing control information and encapsulated data.

Addressing Mechanisms
---------------------

### MAC-Addressing

Insert MAC Address Example Here

Each network interface has a unique MAC address, which is used for layer two communications.

### IP Addressing

#### IPv4

Insert IPv4 Example Here

IPv4 is a 32-bit addressing system used for routing packets across networks.

#### IPv6

Insert IPv6 Example Here

IPv6 is a 128-bit addressing system developed as a successor to IPv4.

TCP / UDP, Transport Mechanisms
-------------------------------

### TCP vs. UDP

Characteristic

TCP - Connection-oriented, Slower

UDP - Connectionless, Faster

### TCP Three-way Handshake

Insert Three-way Handshake Diagram Here

TCP uses a three-way handshake (SYN, SYN-ACK, ACK) to establish a reliable connection.


Networking Primer - Layers 5-7
==============================

We’ve explored how lower-level networking functions operate, so now let’s delve into some upper-layer protocols that power our applications. A network connection relies on multiple applications and services to ensure seamless data transfer between hosts. This section will highlight a few key ones.

HTTP
----

Hypertext Transfer Protocol (HTTP) is a stateless Application Layer protocol that has been in use since 1990. It facilitates the transfer of plaintext data between a client and a server using TCP. The client sends an HTTP request, asking for a resource, and the server responds accordingly, delivering HTML, images, videos, or other content. Typically, HTTP operates on TCP ports 80 or 8000, though it can be configured to use alternate ports or even UDP in specific scenarios.

### HTTP Methods

Various HTTP methods define the actions taken when requesting a URI. Below are some of the most common ones:

Method

Description

HEAD

Requests a response similar to GET but without the message body. Used to retrieve server metadata.

GET

Requests data from a server (e.g., fetching a webpage).

POST

Submits data to a server, such as filling out a form or posting a comment.

PUT

Uploads a resource to the server at a specified URI, either creating or updating it.

DELETE

Removes the specified resource from the server.

TRACE

Echoes the request received by the server, often used for debugging.

OPTIONS

Returns the HTTP methods supported by the server.

CONNECT

Used for establishing a secure connection via proxies.

By default, GET and HEAD must be supported in standard HTTP implementations, while other methods like POST, PUT, and DELETE are optional.

HTTPS
-----

HTTP Secure (HTTPS) is an enhanced version of HTTP that incorporates Transport Layer Security (TLS) or Secure Sockets Layer (SSL) encryption. This ensures that data remains confidential and protected from interception. HTTPS operates over ports 443 and 8443, enabling secure communication between clients and servers.

### TLS Handshake

The TLS handshake follows these steps:

1.  The client initiates a session over port 443, signalling a request for HTTPS communication.
2.  A ClientHello message is sent, negotiating security parameters such as encryption algorithms and session identifiers.
3.  The server and client exchange X.509 certificates for authentication.
4.  A master secret is generated to secure the session.
5.  Security parameters are finalised, and encrypted communication begins.

For more technical details, refer to RFC:2246.

FTP
---

File Transfer Protocol (FTP) enables fast data exchange between computing devices. It operates over TCP ports 20 and 21, with port 21 handling commands and port 20 managing data transfers. While widely used, FTP is inherently insecure, and many users now prefer Secure FTP (SFTP) for encrypted file transfers.

### FTP Commands

Command

Description

USER

Specifies the username for login.

PASS

Sends the user’s password.

PORT

Defines the data transfer port (active mode).

PASV

Switches to passive mode for NAT/firewall compatibility.

LIST

Displays files in the current directory.

RETR

Downloads a file from the FTP server.

QUIT

Ends the session.

For more information, see RFC:959.

SMB
---

Server Message Block (SMB) is commonly used in Windows environments for file and printer sharing. Originally reliant on NetBIOS over UDP ports 137 and 138, modern implementations use TCP over port 445 or NetBIOS over TCP port 139.

### Analysing SMB Traffic

SMB traffic can provide useful security insights:

*   Frequent authentication failures could indicate an attacker attempting to brute-force credentials.
*   Unexpected access to multiple file shares may suggest lateral movement by an unauthorised user.
*   Monitoring SMB activity is crucial for detecting suspicious behaviour in enterprise environments.

<button onclick="history.back()">Go Back</button>
