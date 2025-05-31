---
layout: post
title:  "TB - GNS3 Lab: Spanning Tree Protocol (STP) with Loop Guard"
date:   2025-05-31
author: C. Casquatch
comments: false
tag: ['GNS3', 'root guard', 'Networking', 'spanning-tree']
---

> Loops

**Objective:**
- Configure SW1 as the root bridge for VLAN 1.
- Configure STP so that SW3's gi0/1 interface becomes an Alternate Port.
- Simulate a unidirectional link from SW2 by filtering BPDUs.
- Enable Loop Guard on SW3 and observe its behavior.

---

## Topology Used

![Screenshot topology](/assets/images/GNS3/STPLoopGuard/topology.png)

- SW1 gi0/1 <--> SW2 gi0/0
- SW1 gi0/0 <--> SW3 gi0/0
- SW2 gi0/1 <--> SW3 gi0/1

---

## Step-by-Step Configuration

### Step 1: Set SW1 as the Root Bridge
```bash
conf t
spanning-tree vlan 1 priority 0
end
```

![Screenshot 1](/assets/images/GNS3/STPLoopGuard/sw1root.png)

### Step 2: Lower Priority on SW2 and SW3
```bash
conf t
spanning-tree vlan 1 priority 28672
end
```
Do this on **both SW2 and SW3**.

**[Insert Screenshot: SW2 and SW3 STP priority confirmation]**
![Screenshot 2](/assets/images/GNS3/STPLoopGuard/sw2rootverification.png)
![Screenshot 3](/assets/images/GNS3/STPLoopGuard/sw3rootverification.png)

### Step 3: Adjust STP Path Cost

On **SW3**:
```bash
conf t
interface gi0/0  # SW3 to SW1
 spanning-tree vlan 1 cost 10
interface gi0/1  # SW3 to SW2
 spanning-tree vlan 1 cost 100
end
```

This ensures SW3 prefers the path via SW1.

**[Insert Screenshot: SW3 STP port roles after cost adjustment]**
![Screenshot topology](/assets/images/GNS3/STPLoopGuard/topology.png)

### Step 4: Enable Loop Guard on SW3
```bash
conf t
interface gi0/1
 spanning-tree guard loop
end
```

**[Insert Screenshot: Loop Guard configured]**
![Screenshot topology](/assets/images/GNS3/STPLoopGuard/topology.png)

### Step 5: Simulate Unidirectional Link by Filtering BPDUs
On **SW2**:
```bash
conf t
interface gi0/1
 spanning-tree bpdufilter enable
end
```

This stops SW2 from sending BPDUs to SW3.

**[Insert Screenshot: BPDU filter enabled]**
![Screenshot topology](/assets/images/GNS3/STPLoopGuard/topology.png)

### Step 6: Wait and Check for Inconsistent Port
After ~60 seconds, check SW3:
```bash
show spanning-tree inconsistentports
```

Expected:
```
Name                Interface             Inconsistency
------------------  --------------------  ------------------
VLAN0001            Gi0/1                 Loop Inconsistent
```

**[Insert Screenshot Placeholder: Expected Inconsistent Port Output]**
![Screenshot topology](/assets/images/GNS3/STPLoopGuard/topology.png)

---

## ⚠️ Outcome

Despite completing all configuration steps and verifying that:
- SW1 was elected root
- STP path costs favored SW1 over SW2
- Loop Guard was enabled
- BPDUs were filtered from SW2

... the `show spanning-tree inconsistentports` output **did not show any loop-inconsistent ports**.

### ❓ Why?
This appears to be a limitation of the **Cisco IOSvL2 15.2-1** image in GNS3:

- Alternate port roles were not consistently assigned.
- Loop Guard behavior was not fully triggered.

### 🧪 Conclusion
The lab structure and configs are correct, but due to the limitations in IOSvL2, Loop Guard could not be fully demonstrated in this virtual environment. Consider trying this lab on real switches (e.g. Cisco 2960) or in a more feature-complete emulator.

**[Insert Screenshot: show spanning-tree vlan 1 final status on SW3]**
![Screenshot topology](/assets/images/GNS3/STPLoopGuard/topology.png)

---

## ✅ Commands Summary
```bash
! Set root bridge
spanning-tree vlan 1 priority 0

! Lower other switches' priority
spanning-tree vlan 1 priority 28672

! Set cost
interface gi0/0
 spanning-tree vlan 1 cost 10
interface gi0/1
 spanning-tree vlan 1 cost 100

! Enable Loop Guard
interface gi0/1
 spanning-tree guard loop

! Filter BPDUs (simulate failure)
interface gi0/1
 spanning-tree bpdufilter enable

! Check for loop
show spanning-tree inconsistentports
```

<button onclick="history.back()">Go Back</button>
