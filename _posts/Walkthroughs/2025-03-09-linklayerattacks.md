---
layout: post
title:  "NOTES: Link Layer Attacks: Intermediate Network Traffic Analysis"
date: 2025-03-9
author: C. Casquatch
comments: false
tag: ['traffic analysis', 'networking', 'arp spoofing', 'htb']
---

ARP Spoofing & Abnormality Detection

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



ARP Scanning & Denial-of-Service
================================

The Address Resolution Protocol (ARP) is often associated with man-in-the-middle (MITM) and denial-of-service (DoS) attacks due to its inherent vulnerabilities. While poisoning and spoofing are well-known attack methods, ARP can also be leveraged for reconnaissance. Fortunately, by analysing network traffic, we can identify these tactics and mitigate potential threats.

Indicators of ARP Scanning
--------------------------

### Related PCAP File(s):

*   `ARP_Scan.pcapng`

Signs that may indicate ARP scanning activity include:

*   Sequential ARP requests targeting consecutive IP addresses (e.g., .1, .2, .3, ...)
*   ARP requests directed at non-existent hosts
*   An unusually high volume of ARP traffic originating from a single host

Detecting ARP Scanning
----------------------

Opening `ARP_Scan.pcapng` in Wireshark and applying the filter `arp.opcode` may reveal a single device broadcasting ARP requests across a range of IPs in a structured manner. This systematic probing is a hallmark of ARP scanning and is commonly seen in tools such as Nmap.

Additionally, legitimate hosts on the network will reply to these ARP requests, potentially allowing the attacker to compile a list of active devices.

Identifying Denial-of-Service Attacks
-------------------------------------

### Related PCAP File(s):

*   `ARP_Poison.pcapng`

Once an attacker maps out live hosts, they may escalate their tactics by attempting to manipulate ARP caches. This can lead to service disruption or facilitate a MITM attack.

Indicators of an ARP-based DoS attack include:

*   Repeated ARP responses attempting to associate multiple IP addresses with the same MAC address
*   Modifications to the router's ARP table, potentially redirecting traffic
*   Conflicting ARP records, where a victim device associates a legitimate gateway with an attacker's MAC address

Mitigating ARP Attacks
----------------------

Upon identifying ARP-related anomalies, there are several ways to respond:

1.  **Tracking and Identification:** The attacker's device is physically located somewhere on the network. If detected, isolating it can stop the attack. However, in some cases, the attacking machine may itself be compromised.
2.  **Network Containment:** To limit damage, disconnecting affected devices at the switch or router level can cut off an attacker’s access and prevent further exploitation.

### QUESTION

#### Inspect the ARP_Poison.pcapng file, part of this module's resources, and submit the first MAC address that was linked with the IP 192.168.10.1 as your answer.
ANSWER:




Analysing 802.11 Denial of Service Attacks
==========================================

Related PCAP File(s):
---------------------

*   deauthandbadauth.cap

Wireless network security is an essential aspect of perimeter defence. One of the more overlooked areas is link-layer attacks, particularly those targeting 802.11 (Wi-Fi). These attacks can be exploited to disrupt network availability, making it crucial to continuously monitor and analyse wireless traffic for anomalies.

Capturing 802.11 Traffic
------------------------

To examine raw 802.11 traffic, we need either a Wireless Intrusion Detection/Prevention System (WIDS/WIPS) or a Wi-Fi network adapter capable of monitor mode. Monitor mode allows us to observe raw wireless frames, similar to how promiscuous mode functions in Wireshark.

### Checking Wireless Interfaces

    iwconfig

### Enabling Monitor Mode

We can enable monitor mode using `airmon-ng`:

    sudo airmon-ng start wlan0

Alternatively, we can configure it manually:

    
    sudo ifconfig wlan0 down
    sudo iwconfig wlan0 mode monitor
    sudo ifconfig wlan0 up
        

To verify that monitor mode is enabled:

    iwconfig

### Capturing Wireless Traffic

Using `airodump-ng`, we can begin capturing traffic:

    sudo airodump-ng -c 4 --bssid F8:14:FE:4D:E6:F1 wlan0 -w raw

Understanding Deauthentication Attacks
--------------------------------------

Deauthentication attacks are a common method adversaries use to disrupt Wi-Fi connections. This type of attack is often used for:

*   Capturing WPA handshakes for offline password cracking
*   Causing general network disruption
*   Forcing clients to disconnect and potentially join a rogue network

The attacker spoofs deauthentication frames to appear as if they are coming from a legitimate access point, tricking client devices into disconnecting.

Detecting Deauthentication Attacks
----------------------------------

To identify deauthentication attacks in Wireshark:

    sudo wireshark deauthandbadauth.cap

Filter by access point BSSID:

    wlan.bssid == xx:xx:xx:xx:xx:xx

Filter for deauthentication frames:

    (wlan.bssid == xx:xx:xx:xx:xx:xx) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12)

### Filtering by Reason Code

Common attack tools use reason code 7 for deauthentication:

    (wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 7)

More sophisticated attackers may rotate reason codes to evade detection. Incrementally filtering reason codes can help detect such attacks:

    (wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 1)

    (wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 2)

Preventing Deauthentication Attacks
-----------------------------------

To mitigate the risk of deauthentication attacks, consider implementing the following measures:

*   Enable IEEE 802.11w (Management Frame Protection)
*   Adopt WPA3-SAE for stronger authentication
*   Enhance WIDS/WIPS detection rules

Identifying Failed Authentication Attempts
------------------------------------------

If an attacker repeatedly attempts to connect to a Wi-Fi network, an abnormal number of association requests may be observed. This can be detected using the following filter:

    (wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 0) or (wlan.fc.type_subtype == 1) or (wlan.fc.type_subtype == 11)

### QUESTION

####  Inspect the deauthandbadauth.cap file, part of this module's resources, and submit the total count of deauthentication frames as your answer.
ANSWER: 




Rogue Access Point & Evil-Twin Attacks
======================================

Related PCAP File(s):
---------------------

*   rogueap.cap

* * *

Detecting rogue access points and evil-twin attacks can be challenging due to their covert nature. However, with the right approach, identifying and mitigating these threats becomes much more manageable. Both rogue access points and evil-twin attacks pose serious security risks to wireless networks.

Understanding Rogue Access Points
---------------------------------

A rogue access point is an unauthorised device that an attacker connects directly to a network. This type of attack is used to bypass security controls and segmentation policies, allowing unauthorised access to restricted areas. These access points can be as simple as personal hotspots or tethered devices, and in some cases, they have even been used to compromise air-gapped systems.

What is an Evil-Twin Attack?
----------------------------

Unlike rogue access points, an evil-twin attack does not require a direct connection to the victim’s network. Instead, the attacker sets up a fake wireless access point designed to trick users into connecting. These malicious networks often feature a captive portal or other techniques to intercept sensitive information, such as login credentials.

### Detecting an Evil-Twin with Airodump-ng

One of the quickest ways to spot an evil-twin access point is by filtering for duplicate SSIDs using Airodump-ng:

    sudo airodump-ng -c 4 --essid HTB-Wireless wlan0 -w raw

This command will display any access points broadcasting the same SSID as a legitimate one, which is a common trick used in evil-twin attacks. Attackers often use this method to execute hostile portal attacks, tricking users into entering credentials.

### Identifying Deauthentication Attempts

Attackers frequently use deauthentication attacks to force users off a legitimate network and onto their malicious access point. To investigate further, we can analyse the packet capture file (`rogueap.cap`) and filter for beacon frames:

    (wlan.fc.type == 00) and (wlan.fc.type_subtype == 8)

By examining the RSN (Robust Security Network) information, we can determine whether an access point is legitimate or fraudulent. A genuine access point typically supports WPA2 with AES and TKIP encryption. If these details are missing or altered, we may be dealing with an evil-twin attack.

### Advanced Detection Techniques

While most evil-twin attacks lack encryption, some attackers attempt to evade detection by mimicking legitimate security settings. To counter this, we can inspect additional beacon frame fields, such as vendor-specific information, which is often absent from fake access points.

Identifying a Compromised User
------------------------------

Despite security awareness efforts, some users may unknowingly connect to a rogue network. In open network-style evil-twin attacks, unencrypted traffic can provide clear evidence of a compromise. To isolate traffic related to the attacker’s access point, we can use the following filter:

    (wlan.bssid == F8:14:FE:4D:E6:F2)

If we observe ARP requests originating from a client device on the suspicious network, this suggests the device has connected to the rogue access point. Key information to collect includes:

1.  The device’s MAC address
2.  The device’s hostname

By gathering this data, we can initiate password resets and other security measures to mitigate further risks.

Detecting Rogue Access Points
-----------------------------

Identifying rogue access points is often as simple as reviewing our network’s list of authorised devices. If a hotspot-based rogue access point (such as a Windows hotspot) is being used, scanning for nearby wireless networks can help detect anomalies. A strong, unfamiliar wireless signal, especially one lacking encryption, may indicate that a rogue access point is in use to bypass security controls.

### QUESTION

#### Inspect the rogueap.cap file, part of this module's resources, and enter the MAC address of the Evil Twin attack's victim as your answer.
ANSWER:



<button onclick="history.back()">Go Back</button>
