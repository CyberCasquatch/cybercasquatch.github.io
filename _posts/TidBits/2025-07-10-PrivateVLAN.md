---
layout: post
title:  "TB - GNS3 Lab: Private VLAN"
date:   2025-07-10
author: C. Casquatch
comments: false
tag: ['GNS3', 'PRIVATE-VLAN', 'Networking', 'vlan']
---

> Lab from [Renee Molenaar](https://gns3vault.com/switching/private-vlan)
> NOTE: I know there is a mismatch on interfaces between the fast ethernet and gigabit ethernet in my own topology.

## 🧱 Topology Overview

### Devices and Connections

| Device  | Interface | SW1 Interface |
|---------|-----------|---------------|
| Gateway | fa0/0     | Gi0/0         |
| DNS1    | fa0/0     | Gi0/1         |
| DNS2    | fa0/0     | Gi0/2         |
| WWW1    | fa0/0     | Gi0/3         |
| WWW2    | fa0/0     | Gi1/0         |

### VLANs

| VLAN ID | Type       | Name           | Devices              |
|---------|------------|----------------|----------------------|
| 500     | Primary    | PRIMARY_PVLAN  | All devices          |
| 501     | Community  | COMMUNITY_501  | DNS1, DNS2           |
| 502     | Isolated   | ISOLATED_502   | WWW1, WWW2           |

![Screenshot 1](/assets/images/GNS3/vlans/privatevlan/TOPOLOGY.png)

---

## ⚙️ Switch Configuration (SW1 - IOSvL2)

### 1. Set VTP to Transparent Mode (required)
```
SW1(config)# vtp mode transparent
```

### 2. Configure VLANs
```
SW1(config)# vlan 500
SW1(config-vlan)# private-vlan primary
SW1(config-vlan)# private-vlan association 501,502

SW1(config)# vlan 501
SW1(config-vlan)# private-vlan community

SW1(config)# vlan 502
SW1(config-vlan)# private-vlan isolated
```

### 3. Configure Promiscuous Port (Gateway)
```
SW1(config)# interface gi0/0
SW1(config-if)# switchport mode private-vlan promiscuous
SW1(config-if)# switchport private-vlan mapping 500 501,502
SW1(config-if)# switchport access vlan 500
SW1(config-if)# no shutdown
```

### 4. Configure Community Ports (DNS1, DNS2)
```
SW1(config)# interface gi0/1
SW1(config-if)# switchport mode private-vlan host
SW1(config-if)# switchport private-vlan host-association 500 501
SW1(config-if)# switchport access vlan 500
SW1(config-if)# no shutdown

SW1(config)# interface gi0/2
SW1(config-if)# switchport mode private-vlan host
SW1(config-if)# switchport private-vlan host-association 500 501
SW1(config-if)# switchport access vlan 500
SW1(config-if)# no shutdown
```

### 5. Configure Isolated Port (WWW1, WWWW2)
```
SW1(config)# interface gi0/3
SW1(config-if)# switchport mode private-vlan host
SW1(config-if)# switchport private-vlan host-association 500 502
SW1(config-if)# switchport access vlan 500
SW1(config-if)# no shutdown

SW1(config)# interface gi1/0
SW1(config-if)# switchport mode private-vlan host
SW1(config-if)# switchport private-vlan host-association 500 502
SW1(config-if)# switchport access vlan 500
SW1(config-if)# no shutdown
```

### Confirm VLAN configs

> Showing all Vlans

![Screenshot 2](/assets/images/GNS3/vlans/privatevlan/confirmvlanconfigvlan.png)

> VLAN gi0/0 configuration

![Screenshot 3](/assets/images/GNS3/vlans/privatevlan/vlanconfigg00.png)

> VLAN gi0/1 configuration

![Screenshot 4](/assets/images/GNS3/vlans/privatevlan/vlanconfigg01.png)

> VLAN gi0/2 configuration

![Screenshot 5](/assets/images/GNS3/vlans/privatevlan/vlanconfigg02.png)

> VLAN gi0/3 configuration

![Screenshot 6](/assets/images/GNS3/vlans/privatevlan/vlanconfigg03.png)

> VLAN gi1/0 configuration

![Screenshot 7](/assets/images/GNS3/vlans/privatevlan/vlanconfigg10.png)


## 🌐 Router Configuration (C3640)

> ⚠️ Before assigning IPs, convert router interfaces to Layer 3 by disabling switchport.

### Gateway

```
Gateway(config)# interface fa0/0
Gateway(config-if)# no switchport
Gateway(config-if)# ip address 192.168.1.254 255.255.255.0
Gateway(config-if)# no shutdown
```

### DNS1
```
DNS1(config)# interface fa0/0
DNS1(config-if)# no switchport
DNS1(config-if)# ip address 192.168.1.1 255.255.255.0
DNS1(config-if)# no shutdown
```

### DNS2

```
DNS2(config)# interface fa0/0
DNS2(config-if)# no switchport
DNS2(config-if)# ip address 192.168.1.2 255.255.255.0
DNS2(config-if)# no shutdown
```

### WWW1

```
WWW1(config)# interface fa0/0
WWW1(config-if)# no switchport
WWW1(config-if)# ip address 192.168.1.3 255.255.255.0
WWW1(config-if)# no shutdown
```

### WWW2

```
WWW2(config)# interface fa0/0
WWW2(config-if)# no switchport
WWW2(config-if)# ip address 192.168.1.4 255.255.255.0
WWW2(config-if)# no shutdown
```

## 🧪 Testing Expectations

| Test                    | Expected Result |
| ----------------------- | --------------- |
| DNS1 <-> DNS2           | ✅ Success       |
| All devices <-> Gateway | ✅ Success       |
| WWW1 <-> WWW2           | ❌ Fail          |
| WWW <-> DNS             | ❌ Fail          |

Use ping from each router to validate.

> Pinging from DNS2 to confirm success or fail

![Screenshot 8](/assets/images/GNS3/vlans/privatevlan/DNS2pingtest.png)

> Pinging from WWW2 to confirm success or fail

![Screenshot 9](/assets/images/GNS3/vlans/privatevlan/WWW2egping.png)


## 🔍 Useful Verification Commands
```
show vlan private-vlan
show interfaces switchport
show interfaces gi0/1 switchport
show interfaces gi0/3 switchport
```

## ⚠️ Troubleshooting Notes

### ❗ Error: Private VLANs require VTP Transparent Mode
Error Message:
%Private VLANs can only be configured when VTP is in transparent/off modes...

Fix:
Run vtp mode transparent on the switch.

### ❗ Error: Can't Assign IP to L2 Interface
Error Message:
% IP addresses may not be configured on L2 links.

Fix:
Use no switchport before assigning IP on router interfaces.

<button onclick="history.back()">Go Back</button>
