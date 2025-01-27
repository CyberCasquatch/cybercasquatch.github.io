---
layout: post
title:  "Intro to threat hunting & hunting with Elastic; Threat Hunting Glossery"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['threat hunting', 'elastic', 'htb']
---

> Essential Threat Hunting Terms

========================================================================

In the ever-evolving world of cybersecurity, staying informed about critical concepts and frameworks is key to effective threat hunting. This glossary dives into the most important terms, providing insights to help you understand and apply them in your cybersecurity journey.

Adversary
---------

An _adversary_ in Cyber Threat Intelligence (CTI) refers to an entity actively seeking unauthorized access to your organization. Motivations range from financial gain and espionage to intellectual property theft. Adversaries vary in technical expertise and can be categorized as:

*   **Cyber Criminals:** Motivated by profit.
*   **Insider Threats:** Disgruntled employees or collaborators.
*   **Hacktivists:** Driven by ideological goals.
*   **State-Sponsored Operators:** Backed by nation-states, often targeting high-value sectors.

Advanced Persistent Threat (APT)
--------------------------------

APTs represent organized, resource-rich groups capable of conducting long-term operations. While "advanced" doesn’t always mean cutting-edge techniques, it highlights their strategic planning.

*   **Advanced:** Thoughtful planning and resource allocation.
*   **Persistent:** Long-term dedication to objectives.

APTs often target government agencies, healthcare systems, and defense infrastructures.

Tactics, Techniques, and Procedures (TTPs)
------------------------------------------

A military-inspired framework that defines how adversaries operate:

*   **Tactics:** The "why" – strategic goals.
*   **Techniques:** The "how" – methods for achieving tactics.
*   **Procedures:** The "what" – step-by-step actions.

Understanding TTPs aids in identifying Indicators of Compromise (IOCs) and predicting adversary behavior.

Indicators of Compromise (IOCs)
-------------------------------

IOCs are the digital footprints of malicious activity. They include:

*   **Hashes:** Unique identifiers of malicious files.
*   **IP Addresses:** Potential sources of attacks (but often masked).
*   **Domain Names:** Associated with adversary-controlled servers.
*   **Artifacts:** Residual traces in network or host systems, such as packet logs or registry entries.

The Pyramid of Pain
-------------------

Created by David Bianco, this hierarchy demonstrates how certain indicators impact adversaries:

1.  **Hash Values:** Easy to change, low impact on attackers.
2.  **IP Addresses:** Can be masked or rotated frequently.
3.  **Domain Names:** Altered using dynamic DNS or generation algorithms.
4.  **Artifacts:** Challenging to hide without operational disruption.
5.  **TTPs:** Most impactful but hardest to uncover.

By focusing on higher-level indicators, defenders can force adversaries to overhaul their operations.

Threat
------

Threats are defined by three factors:

*   **Intent:** The adversary's goals (e.g., espionage, financial gain).
*   **Capability:** Their tools, resources, and expertise.
*   **Opportunity:** Conditions that enable exploitation (e.g., stolen credentials or unpatched systems).

Campaign
--------

A _campaign_ is a series of incidents linked by similar TTPs. Understanding campaigns requires extensive analysis to uncover patterns and objectives.

Diamond Model of Intrusion Analysis
-----------------------------------

This framework offers a structured approach to understanding cyber threats, emphasizing four interconnected components:

*   **Adversary:** Who is behind the attack?
*   **Capability:** What tools and methods are they using?
*   **Infrastructure:** What resources enable their operations?
*   **Victim:** Who is being targeted?

This model complements the Cyber Kill Chain by focusing on relationships between adversary actions and victim impact.

**Example:** A financial institution (victim) targeted by a cybercriminal group (adversary) using spear-phishing emails (capability) sent from a botnet (infrastructure).

Why Understanding These Concepts Matters
----------------------------------------

Knowledge of these terms and frameworks helps cybersecurity professionals:

*   **Detect:** Adversary behavior more effectively.
*   **Analyze:** Patterns in incidents and campaigns.
*   **Mitigate:** Risks through informed decision-making.

By mastering these foundational concepts, you can build a robust threat-hunting strategy and make a meaningful impact in defending your organization.

<button onclick="history.back()">Go Back</button>

