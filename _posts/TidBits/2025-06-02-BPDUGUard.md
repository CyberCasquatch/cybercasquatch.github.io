---
layout: post
title:  "TB - GNS3 Lab: Spanning Tree Protocol (STP) with BPDUGuard"
date:   2025-06-02
author: C. Casquatch
comments: false
tag: ['GNS3', 'bpduguard', 'Networking', 'spanning-tree']
---

> Lab from [Renee Molenaar](https://gns3vault.com/switching/spanning-tree-bpdu-guard)


![topology](/assets/images/GNS3/STPBPDUguard/topology.png)

## Lab Goals
- Ensure all switches run PVST (default on most Cisco switches).
- Configure BPDU Guard so that if Router Neo (R1) sends a BPDU, the interface on SW1 will be disabled.
- After 400 seconds, the interface should automatically re-enable.

## Topology
- SW1 Gi0/0 connected to R1 Fa0/0

---

## Step 1: Confirm PVST is Enabled
PVST is usually the default on Cisco IOS switches.

```
SW1# show spanning-tree summary
```

Look for “PVST” or “Per-VLAN” in the output.

---

![Screenshot 1](/assets/images/GNS3/STPBPDUguard/sct1.png)

## Step 2: Configure the Switch Port as a PortFast Edge Port

BPDU Guard only works on PortFast ports (intended for end devices).

```
SW1(config)# interface GigabitEthernet0/0
SW1(config-if)# spanning-tree portfast
```

> ⚠️ Only use PortFast on ports connected to end devices, not other switches.

---

## Step 3: Enable BPDU Guard on the Interface

```
SW1(config-if)# spanning-tree bpduguard enable
```
![Screenshot 1](/assets/images/GNS3/STPBPDUguard/bpduguardstatus.png)

---

## Step 4: Configure Automatic Recovery After 400 Seconds

```
SW1(config)# errdisable recovery cause bpduguard
SW1(config)# errdisable recovery interval 400
```

This configuration allows the switch to automatically bring the port back up after 400 seconds if it was shut down due to a BPDU.

---

## Step 5: Verification Commands

Check BPDU Guard status:

```
SW1# show spanning-tree interface gi0/0 detail
```
![Screenshot 3](/assets/images/GNS3/STPBPDUguard/sct3.png)

Check if the port was err-disabled:

```
SW1# show interface status err-disabled
```

Check recovery configuration:

```
SW1# show errdisable recovery
```


<button onclick="history.back()">Go Back</button>
