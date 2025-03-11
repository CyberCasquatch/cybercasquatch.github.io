---
layout: post
title:  "Skills Assessment: Windows Attacks & Defense"
date: 2025-03-4
author: C. Casquatch
comments: false
tag: ['windows', 'skills assessment', 'htb']
---

* * *

Description
-----------

In continuation of the PKI-related attack from the previous section, another attack technique that can be exploited is relaying to ADCS to obtain a certificate. This is known as ESC8. In the previous section, we used PrinterBug and Coercer to force computers to connect to other systems. In this scenario, we will again use PrinterBug, and with the reverse connection we receive, we will relay the traffic to ADCS to obtain a certificate for the machine we coerced.

Attack
------

To begin, we configure `NTLMRelayx` to forward incoming connections to the HTTP endpoint of our Certificate Authority (CA). We will also specify that we wish to obtain a certificate for the Domain Controller, using a default template in AD which is typically used for client authentication. The `--adcs` switch tells `NTLMRelayx` to parse and display the certificate once received.

Next, we need to get the Domain Controller to connect back to us. We’ll exploit the Print Spooler vulnerability to force a reverse connection (similar to previous labs). In this case, we will compel DC2 to connect to our Kali machine, while `NTLMRelayx` is listening in a separate terminal.

Once we switch back to the terminal running `NTLMRelayx`, we will see that the incoming request from `DC2$` was relayed successfully, and the certificate has been obtained.

We will then copy the base64-encoded certificate and switch to the Windows machine. Using `Rubeus`, we can apply the certificate to authenticate and obtain a TGT. This time, the certificate is in the correct format.

We now have a TGT for the Domain Controller `DC2`, meaning we have effectively become `DC2`. As a Domain Controller, we can trigger DCSync with `Mimikatz`.

Prevention
----------

The attack above was successful because:

*   We successfully coerced `DC2`.
*   The ADCS web enrolment did not enforce HTTPS (if HTTPS was used, the relay would fail, and we wouldn't be able to request a certificate).

Given the variety of PKI-related escalation techniques available, it’s highly recommended to regularly scan the environment with tools like Certify or others to identify potential vulnerabilities.

Detection
---------

This attack provides several detection opportunities. Beginning with the certificate request by `NTLMRelayx`, the CA logs both the request and the certificate issuer in event IDs 4886 and 4887, respectively.

What’s noticeable here is the template name is included as part of the request. However, this is absent when the certificate request is made directly by the Domain Controller (not via relaying). There may be exceptions in certain environments, so it’s crucial to evaluate whether this could serve as an indicator for coercion or relaying attacks targeting ADCS.

During the attack, after using the obtained certificate to request a Kerberos TGT, we will observe event ID 4768, which indicates an attempt to authenticate with a certificate from an IP address that is not the Domain Controller's.

Finally, when we execute DCSync using `Mimikatz`, event ID 4624 will be logged, indicating that authentication was successful from an IP address that is not the Domain Controller’s.

### QUESTIONS

#### Replicate the attack described in this section and view the related 4886 and 4887 logs. Enter the name shown in the Requester field as your answer. (Format: EAGLE\....)
ANSWER:


<button onclick="history.back()">Go Back</button>
