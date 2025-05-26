---
layout: post
title:  "TB Reaper"
date:   2025-05-17
author: C. Casquatch
comments: false
tags: ['ntlm', 'LLMNR', 'htb', 'noxious', 'wireshark']
---
> LLMNR Poisoning Walkthrough

## Scenario Overview

The IDS device alerted us to a possible rogue device on the internal Active Directory network. It also flagged unusual LLMNR traffic directed towards `Forela-WKstn002` (IP: `172.17.79.136`). As a Network Forensics expert, you are asked to investigate potential LLMNR poisoning on the Active Directory VLAN.

---

## What is LLMNR Poisoning?

[TCM Security’s article](https://tcm-sec.com/llmnr-poisoning-and-how-to-prevent-it/) provides an excellent explanation:

- **LLMNR (Link-Local Multicast Name Resolution)** is used when DNS fails to resolve hostnames on a local network.
- It lacks authentication, which allows attackers to spoof replies and intercept sensitive data (like usernames and password hashes).
- Tools like **Responder** automate these attacks, capturing hashes that can be cracked offline with tools like **Hashcat**.
- Mitigation includes disabling LLMNR/NBT-NS, enforcing strong password policies, and patching systems regularly.

---

## Step-by-Step Analysis

### Step 1: Identify Most Active IPs

Open `Statistics -> Endpoints` in Wireshark to view the top talkers. Look for IPs close to `172.17.79.136` to understand the internal IP layout.

![Screenshot 1](/assets/images/noxious/screenshot1.png)

---

### Step 2: Find the Malicious IP Address

- Filter LLMNR traffic using: `udp.port == 5355`
- Look for responses to queries from `172.17.79.136`. Only **one IP** responds – this is our suspected rogue machine.
- This IP **is not** the domain controller.

![Screenshot 2](/assets/images/noxious/screenshot2.png)

---

### Step 3: Identify Hostname of the Rogue Device

- Use filter: `ip.addr == 172.17.79.135 && dhcp`
- Check DHCP packets for the hostname value.

![Screenshot 3](/assets/images/noxious/screenshot3.png)

---

### Step 4: Confirm Hash Capture & Find the Username

- Use `smb2` to locate SMB traffic.
- Then filter further with `ntlmssp`.
- Look for `NTLMSSP_AUTH` packets – you’ll find the **username** here.

![Screenshot 4](/assets/images/noxious/screenshot4.png)
![Screenshot 5](/assets/images/noxious/screenshot5.png)
![Screenshot 6](/assets/images/noxious/screenshot6.png)

---

### Step 5: Find the First Time the Hash Was Captured

- Make sure your time format is set to **UTC**.
- Check the timestamp on the first `NTLMSSP_AUTH` packet.

![Screenshot 7](/assets/images/noxious/screenshot7.png)

---

### Step 6: Identify the Victim’s Typo

- Look for the hostname the victim tried to access.
- They mistyped `DC01` as `DCC01`.

**Hint:** Domain Controller Controller? Oops.

---

### Step 7: Extract NTLM Server Challenge Value

- Add `ntlmssp` filter again.
- Go to the `NTLMSSP_CHALLENGE` packet.
- Path: `SMB2 -> Session Setup Response -> Security Blob -> NTLM Server Challenge`

![Screenshot 8](/assets/images/noxious/screenshot8.png)

---

### Step 8: Find the NTProofStr Value

- Locate the `NTLMSSP_AUTH` packet just after the challenge.
- Navigate similarly through the protocol stack to find `NTProofStr`.

![Screenshot 9](/assets/images/noxious/screenshot9.png)
![Screenshot 10](/assets/images/noxious/screenshot10.png)

---

### Step 9: Crack the Captured Hash

- Construct your hash in the following format:

  ```
  Username::Domain:ServerChallenge:NTProofStr:NTLMv2Response (without first 16 bytes)
  ```

- Save to a file and use:

  ```bash
  hashcat -a 0 -m 5600 /path/to/file/hashfile.txt /path/to/file/rockyou.txt
  ```

**Note:** Don’t forget the colon after `NTProofStr` – it’ll save you 30 minutes of frustration.

![Screenshot 11](/assets/images/noxious/screenshot11.png)
![Screenshot 12](/assets/images/noxious/screenshot12.png)

---

### Step 10: Identify the Target File Share

- Use `smb2` filter.
- Look for a **non-default file share** with a tree connect/disconnect.
- The name should suggest why the attacker was interested.

![Screenshot 13](/assets/images/noxious/screenshot13.png)

---
<button onclick="history.back()">Go Back</button>
