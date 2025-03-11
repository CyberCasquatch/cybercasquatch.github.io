---
layout: post
title:  "Skills Assessment: Working with IDS/IPS (Suricata, Zeek, Snort)"
date: 2025-03-9
author: C. Casquatch
comments: false
tag: ['snort', 'zeek', 'suricata', 'ids', 'ips', 'htb']
---

All of the wording here is directly taken from the skills assessment of Snort, Zeek, and Suricata as I find them most clear and not able to dilute them with my own wording to make as much sense. 

# Skills Assessment - Suricata

## Suricata Rule Development Exercise: Detecting WMI Execution (Through WMIExec)

- **PCAP source**: [https://github.com/elcabezzonn/Pcaps](https://github.com/elcabezzonn/Pcaps)
- **Attack description and possible detection points**: [https://labs.withsecure.com/publications/attack-detection-fundamentals-discovery-and-lateral-movement-lab-5](https://labs.withsecure.com/publications/attack-detection-fundamentals-discovery-and-lateral-movement-lab-5)

### Overview

Windows Management Instrumentation (WMI) is a powerful feature in the Windows operating system that allows for management tasks, such as the execution of code or management of devices, both locally and remotely. As you might expect, this can be a very enticing tool for attackers who are seeking to execute malicious activities remotely.

To detect WMI execution (through wmiexec) over the network, we need to focus on the **SMB** (Server Message Block) and **DCOM** (Distributed Component Object Model) protocols, which are the primary means by which remote WMI execution is accomplished.

### Attack Example

One method an attacker might use is to create a `Win32_Process` via the WMI service. In this instance, the attacker would:

1. Create an instance of `Win32_ProcessStartup`
2. Set its properties to control the environment of the new process
3. Call the `Create` method to start a new process such as `cmd.exe` or `powershell.exe`

### QUESTION

#### There is a file named pipekatposhc2.pcap in the /home/htb-student/pcaps directory, which contains network traffic related to WMI execution. Add yet another content keyword right after the msg part of the rule with sid 2024233 within the local.rules file so that an alert is triggered and enter the specified payload as your answer. Answer format: C____e
ANSWER:



# Skills Assessment - Snort

## Snort Rule Development Exercise: Detecting Overpass-the-Hash

- **PCAP source**: [https://github.com/elcabezzonn/Pcaps](https://github.com/elcabezzonn/Pcaps)
- **Attack description and possible detection points**: [http://www.labofapenetrationtester.com/2017/08/week-of-evading-microsoft-ata-day2.html](http://www.labofapenetrationtester.com/2017/08/week-of-evading-microsoft-ata-day2.html)

### Overview

**Overpass-the-Hash** (Pass-the-Key) is a type of attack where an adversary gains unauthorized access to resources by using a stolen NTLM (NT LAN Manager) hash or Kerberos key, without needing to crack the password from which the hash was derived. The attack involves using the hash to create a **Kerberos TGT** (Ticket-Granting Ticket) to authenticate to Active Directory (AD).

When the adversary utilizes Overpass-the-Hash, they have the NTLM hash of the user's password, which is used to craft an **AS-REQ** (Authentication Service Request) to the Key Distribution Center (KDC). To appear authentic, the AS-REQ contains a **PRE-AUTH** field, which contains an encrypted timestamp (**Enc-Timestamp**). This is normally used by a legitimate client to prove knowledge of the user's password, as it is encrypted using the user's password hash.

In this attack scenario, the hash used to encrypt the timestamp is not derived from the actual password but rather it is the stolen NTLM hash. More specifically, in an Overpass-the-Hash attack, the attacker doesn't use this hash to encrypt the Enc-Timestamp. Instead, the attacker directly uses the stolen NTLM hash to compute the Kerberos AS-REQ, bypassing the usual Kerberos process that would involve the user's password and the Enc-Timestamp. The attacker essentially "overpasses" the normal password-based authentication process, hence the name **Overpass-the-Hash**.

### Detection Indicators

One key aspect of this type of attack that we can leverage for detection is the **encryption type** used for the Enc-Timestamp. A standard AS-REQ from a modern Windows client will usually use the **AES256-CTS-HMAC-SHA1-96** encryption type for the Enc-Timestamp, but an Overpass-the-Hash attack using the older NTLM hash will use the **RC4-HMAC** encryption type. This discrepancy can be used as an indicator of a potential attack.

### QUESTION

#### There is a file named wannamine.pcap in the /home/htb-student/pcaps directory, which contains network traffic related to the Overpass-the-hash technique which involves Kerberos encryption type downgrading. Replace XX with the appropriate value in the last content keyword of the rule with sid XXXXXXX within the local.rules file so that an alert is triggered as your answer.
ANSWER:



# Skills Assessment - Zeek

## Intrusion Detection With Zeek: Detecting Gootkit's SSL Certificate

- **PCAP source**: [https://www.malware-traffic-analysis.net/2016/07/08/index.html](https://www.malware-traffic-analysis.net/2016/07/08/index.html)
- **Attack description and possible detection points**: [https://www.malware-traffic-analysis.net/2016/07/08/index.html](https://www.malware-traffic-analysis.net/2016/07/08/index.html) <-- Focus on the SSL certificate parts.

### Overview

**Neutrino**, a notorious exploit kit, and **Gootkit**, a potent banking trojan, collaborated in the past to perpetrate cyberattacks.

The Neutrino exploit kit opened the gate, and then Gootkit began to communicate over the network using SSL/TLS encryption. It's within these encrypted communications that we encountered a particularly striking detail - the **SSL certificates** used by Gootkit contained the **Common Name (CN)** "My Company Ltd."

Cybercriminals frequently employ self-signed or non-trusted CA issued certificates to foster encrypted communication. These certificates often feature bogus or generic details. In this case, the common name "My Company Ltd." stands out as an anomaly we can use to identify this specific Gootkit infection delivered via the Neutrino exploit kit.

### Detection

Review the previously referenced resource that discusses the network traces resulting from Gootkit communications, and then proceed to address the following question.

### QUESTION

#### There is a file named neutrinogootkit.pcap in the /home/htb-student/pcaps directory, which contains network traffic related to the Neutrino exploit kit sending Gootkit malware. Enter the x509.log field name that includes the "MyCompany Ltd." trace as your answer.
ANSWER:


<button onclick="history.back()">Go Back</button>
