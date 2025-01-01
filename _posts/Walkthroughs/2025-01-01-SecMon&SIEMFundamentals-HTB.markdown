---
layout: post
title:  "Security Monitoring & SIEM Fundamentals Notes for HTB Soc Path"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['soc', 'siem', 'security monitoring', 'incident response']
---

# Mastering Security Operations: SIEM, SOC, and Incident Response Essentials

In the ever-evolving landscape of cybersecurity, understanding the foundations of Security Information and Event Management (SIEM) and Security Operations Centres (SOC) is crucial. This post covers the essentials of SIEM, SOC, the MITRE ATT&CK framework, SIEM use case development, and triaging—providing you with a clear roadmap to mastering these critical concepts.

## What is SIEM?

Security Information and Event Management (SIEM) combines real-time monitoring, data aggregation, and analytics to detect, investigate, and respond to security threats. It serves as the nerve centre of modern cybersecurity, allowing organisations to process vast volumes of data from multiple sources.

### Key Functions of SIEM:
- **Data Collection:** Aggregates logs from firewalls, servers, applications, and more.
- **Correlation and Analysis:** Identifies patterns and relationships to detect anomalies.
- **Incident Detection:** Uses preconfigured rules and machine learning to flag potential threats.
- **Incident Response:** Enables rapid triaging and remediation of security events.

## The Role of the SOC

A Security Operations Centre (SOC) is the operational hub where cybersecurity professionals monitor, analyse, and defend against threats in real time.

### Key Components of a SOC:
- **People:** Analysts, engineers, and incident responders form the backbone of SOC operations.
- **Processes:** Clearly defined workflows for monitoring, detection, and incident handling.
- **Technology:** Tools like SIEM platforms and threat intelligence feeds to enhance visibility and automation.

The SOC relies heavily on the MITRE ATT&CK Framework to structure its defences.

## MITRE ATT&CK: Guiding Security Operations

The MITRE ATT&CK Framework is a comprehensive knowledge base that categorises tactics, techniques, and procedures (TTPs) used by attackers. It provides:

- **Tactics:** High-level attack objectives (e.g., Persistence, Privilege Escalation).
- **Techniques:** Specific methods attackers use (e.g., Credential Dumping).
- **Procedures:** Real-world examples of how techniques are executed.

SOC teams use this framework to map attacks, understand adversary behaviour, and improve detection mechanisms.

## Developing SIEM Use Cases

A SIEM use case defines how to detect and respond to specific threats using SIEM tools.

### Steps for Developing Use Cases:
1. **Identify Objectives:** Define what threats or behaviours to monitor (e.g., unauthorised logins).
2. **Gather Data:** Determine which logs and events are needed for detection.
3. **Write Detection Rules:** Create queries or rules to flag suspicious activities.
4. **Test and Optimise:** Validate the use case with simulated attacks and refine based on results.
5. **Create Visualisations:** Use dashboards to represent data trends and anomalies.

## The Triaging Process

Triaging is the first line of defence when handling alerts in a SOC. It involves:

- **Prioritisation:** Classify alerts based on severity, relevance, and potential impact.
- **Initial Analysis:** Investigate the context of the alert—who, what, when, and where.
- **Validation:** Confirm whether the alert is legitimate or a false positive.
- **Escalation or Resolution:** Escalate critical incidents to higher-tier analysts or resolve benign ones.

Effective triaging minimises response time and ensures critical threats receive immediate attention.

## Conclusion

Mastering SIEM, SOC operations, and incident handling is essential for today’s cybersecurity professionals. By understanding SIEM fundamentals, leveraging SOC workflows, applying the MITRE ATT&CK Framework, developing robust use cases, and refining your triaging process, you’ll be well-equipped to handle the challenges of modern cybersecurity.

For those looking to dive deeper, Hack The Box Academy's modules on Security Monitoring and SIEM Fundamentals offer practical, hands-on training to reinforce these concepts.
