---
layout: post
title:  "TB - GNS3 Lab: PAgP LACP Etherchannel"
date:   2025-07-11
author: C. Casquatch
comments: false
tag: ['GNS3', 'pagp', 'Networking', 'lacp', 'etherchannel']
---

> Lab from [Renee Molenaar](https://gns3vault.com/switching/pagp-lacp-etherchannel)


## Topology

```
SW1 <--> SW2 : Gi0/0, Gi0/1
SW1 <--> SW3 : Gi0/3, Gi0/1
SW2 <--> SW3 : Gi0/2, Gi0/3
```
![Screenshot 1](/assets/images/GNS3/Etherchannel/topology.png)

## Lab Goals

1. Configure PAgP between SW1 and SW2:  
   - SW1 = desirable (active)
   - SW2 = auto (passive)
2. Configure LACP between SW1 and SW3:  
   - SW1 = active
   - SW3 = passive
3. Configure static EtherChannel between SW2 and SW3 (no negotiation protocol).
4. Set EtherChannel on SW1 to use destination MAC address for load-balancing.

---

## Step-by-Step Configuration

### 1. PAgP EtherChannel: SW1 ↔ SW2

#### On SW1:
```
conf t
interface range gi0/0 - 1
 channel-group 1 mode desirable
exit
interface port-channel 1
 switchport
 switchport mode access
exit
```

#### On SW2:
```
conf t
interface range gi0/0 - 1
 channel-group 1 mode auto
exit
interface port-channel 1
 switchport
 switchport mode access
exit
```

---

### 2. LACP EtherChannel: SW1 ↔ SW3

#### On SW1:
```
conf t
interface range gi0/3, gi0/1
 channel-group 2 mode active
exit
interface port-channel 2
 switchport
 switchport mode access
exit
```

#### On SW3:
```
conf t
interface range gi0/0 - 1
 channel-group 2 mode passive
exit
interface port-channel 2
 switchport
 switchport mode access
exit
```

---

### 3. Static EtherChannel: SW2 ↔ SW3

#### On SW2:
```
conf t
interface range gi0/2 - 3
 channel-group 3 mode on
exit
interface port-channel 3
 switchport
 switchport mode access
exit
```

#### On SW3:
```
conf t
interface range gi0/2 - 3
 channel-group 3 mode on
exit
interface port-channel 3
 switchport
 switchport mode access
exit
```

---

### 4. Load-Balancing (SW1)

#### On SW1:
```
conf t
port-channel load-balance dst-mac
exit
```

---

## Verification Commands

```
show etherchannel summary
```
![Screenshot 2](/assets/images/GNS3/Etherchannel/etherchannelsumSW1.png)

![Screenshot 3](/assets/images/GNS3/Etherchannel/etherchannelsumSW2.png)

```
show interfaces port-channel 1
show interfaces port-channel 2
show interfaces port-channel 3
```
![Screenshot 4](/assets/images/GNS3/Etherchannel/SW1showpc1.png)

![Screenshot 5](/assets/images/GNS3/Etherchannel/SW3showpc2.png)

![Screenshot 6](/assets/images/GNS3/Etherchannel/SW2showpc3.png)

```
show running-config | include channel-group
```
![Screenshot 7](/assets/images/GNS3/Etherchannel/SW1runningconfig.png)

![Screenshot 8](/assets/images/GNS3/Etherchannel/SW2runningconfig.png)

![Screenshot 9](/assets/images/GNS3/Etherchannel/SW3runningconfig.png)

```
show etherchannel load-balance
```
![Screenshot 10](/assets/images/GNS3/Etherchannel/SW1loadbalancing.png)

---

## Notes

- Ensure all interfaces in a channel have matching speed, duplex, switchport mode.
- Shut/no shut interfaces if channels don't form.
- Use the following for troubleshooting:
  - `show pagp neighbor` (PAgP)
  - `show lacp neighbor` (LACP)
  - `show log`

---

<button onclick="history.back()">Go Back</button>
