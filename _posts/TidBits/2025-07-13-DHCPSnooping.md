---
layout: post
title:  "TB - GNS3 Lab: DHCP Snooping"
date:   2025-07-13
author: C. Casquatch
comments: false
tag: ['GNS3', 'dhcp snooping', 'Networking']
---

> Lab from [Renee Molenaar](https://gns3vault.com/switching/dhcp-snooping)

## Topology

![Screenshot 1](/assets/images/GNS3/dhcpsnooping/topology.png)

## Lab Goals

1. Configure IP addresses:
   - Attacker: `192.168.123.253`
   - DHCP Server: `192.168.123.254`
2. Configure a DHCP pool on the DHCP server router.
3. Enable DHCP snooping globally on SW1.
4. Configure trusted and untrusted interfaces on SW1.
5. Limit the client port to 10 DHCP packets per second.
6. Configure the attacker as a rogue DHCP server to test snooping.

---

## Step-by-Step Configuration

### 1. Configure IP Addresses

#### On Attacker Router:
```
conf t
interface f0/0
no switchport #You don't need this line if you are using real hardware#
ip address 192.168.123.253 255.255.255.0
no shutdown
exit
```

#### On DHCP Server Router:
```
conf t
interface f0/0
no switchport #You don't need this line if you are using real hardware#
ip address 192.168.123.254 255.255.255.0
no shutdown
exit
```

---

### 2. Configure DHCP Pool on DHCP Server

```
conf t
ip dhcp excluded-address 192.168.123.1 192.168.123.10
ip dhcp pool LABPOOL
network 192.168.123.0 255.255.255.0
default-router 192.168.123.254
dns-server 8.8.8.8
exit
```

---

### 3. Enable DHCP Snooping Globally on SW1

```
conf t
ip dhcp snooping
ip dhcp snooping vlan 1
exit
```

---

### 4. Configure Trusted/Untrusted Interfaces and Rate Limiting

```
interface gi0/2    ! Trusted (to DHCP server)
 ip dhcp snooping trust
exit

interface gi0/0    ! Untrusted (to attacker)
 no ip dhcp snooping trust
exit

interface gi0/1    ! Untrusted (to client)
 no ip dhcp snooping trust
 ip dhcp snooping limit rate 10
exit
```

---

### 5. (Optional) Set all ports to access mode

```
interface range gi0/0 - 2
switchport mode access
switchport access vlan 1
exit
```

---

## Verification

```
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping statistics
```

![Screenshot 2](/assets/images/GNS3/dhcpsnooping/verificationcommands1.png)

---

## Testing DHCP Snooping

### A. DHCP Server Test (Client should get an IP)

On Client Router (if DHCP is supported):
```
conf t
interface f0/0
 ip address dhcp
 no shutdown
```

If not supported, use a VPCS or TinyCore Linux VM and run `dhclient` or `udhcpc`.

---

### B. Rogue DHCP Server (Attacker)

```
conf t
ip dhcp excluded-address 192.168.123.240 192.168.123.254
ip dhcp pool EVILPOOL
network 192.168.123.0 255.255.255.0
default-router 192.168.123.253
dns-server 1.1.1.1
exit
```

DHCP snooping should block the attacker’s DHCP replies.


<button onclick="history.back()">Go Back</button>
