---
layout: post
title:  "Intro to threat hunting & hunting with Elastic; Threat intelligence fundamentals"
date: 2025-01-27
author: C. Casquatch
comments: false
tag: ['threat hunting', 'elastic', 'htb']
---

> What I Learned: Threat Intelligence Fundamentals


================================================

Understanding Cyber Threat Intelligence (CTI)
---------------------------------------------

Cyber Threat Intelligence (CTI) serves as a vital component in modern cybersecurity strategies. The primary goal is to shift our defensive posture from reactive to proactive, providing our Security Operations Centre (SOC) with actionable insights.

CTI revolves around four critical principles:

*   **Relevance:** Information must pertain to the organisation's needs. For example, a vulnerability in software we do not use holds little significance.
*   **Timeliness:** Intelligence must be communicated promptly. Fresh data is far more valuable than outdated insights.
*   **Actionability:** The intelligence should yield clear steps for defensive measures. Unactionable intelligence is counterproductive.
*   **Accuracy:** Intelligence must be verified to avoid wasting resources. If accuracy is uncertain, confidence indicators should be included.

When these principles align, CTI helps organisations:

*   Anticipate adversary campaigns and operations.
*   Develop mitigation measures informed by adversary Tactics, Techniques, and Procedures (TTPs).
*   Provide decision-makers with the information needed to manage risks effectively.

Threat Intelligence vs. Threat Hunting
--------------------------------------

Though interconnected, Threat Intelligence and Threat Hunting are distinct disciplines:

*   **Threat Intelligence:** Predictive, focusing on understanding adversary objectives and preparing for potential attacks.
*   **Threat Hunting:** Both reactive and proactive, involving searching for evidence of adversary activity in response to an incident or proactively scanning for signs of compromise.

The two disciplines reinforce each other: intelligence informs hunting activities, and hunting results enhance intelligence.

The Criteria for Effective CTI
------------------------------

For CTI to be effective, it must be actionable, timely, relevant, and accurate. High-quality CTI offers:

*   Insights into threats to the organisation and its partners.
*   A deeper understanding of the organisation’s network and potential blind spots.
*   Support for leadership to minimise risks and develop contingency plans.

Types of Cyber Threat Intelligence
----------------------------------

CTI can be classified into three categories, each serving different organisational levels:

*   **Strategic Intelligence:** High-level insights for executives and decision-makers, addressing the "who" and "why."
*   **Operational Intelligence:** Mid-level details for management, focusing on the "how" and "where."
*   **Tactical Intelligence:** Immediate, actionable information for network defenders, including specific Indicators of Compromise (IOCs).

Interpreting Tactical Threat Intelligence Reports
-------------------------------------------------

Analysing threat intelligence reports requires a structured approach. For example, in a report about an Emotet malware campaign, the process might include:

1.  **Understanding the Report’s Scope:** Identify the campaign’s goals and assess its relevance to the organisation.
2.  **Classifying IOCs:** Organise indicators (e.g., IP addresses, file hashes) by type and context.
3.  **Mapping the Attack Lifecycle:** Use frameworks like MITRE ATT&CK to understand the attacker’s methods and predict next steps.
4.  **Validating IOCs:** Cross-check against trusted sources and consider the age of the indicators to ensure accuracy.
5.  **Implementing IOCs:** Update security systems with relevant indicators, ensuring changes are properly managed and documented.
6.  **Proactive Threat Hunting:** Search for signs of compromise, focusing on broader TTPs alongside specific IOCs.
7.  **Continuous Monitoring:** Monitor for hits on implemented indicators and refine defences based on findings.

This comprehensive approach ensures that intelligence reports translate into effective defensive actions, reducing organisational risk.

Conclusion
----------

The knowledge gained from Threat Intelligence Fundamentals underscores the importance of aligning timely, relevant, actionable, and accurate intelligence with organisational defence strategies. By combining CTI with proactive threat hunting and structured analysis, we can significantly strengthen our security posture and better anticipate adversary activity.

<button onclick="history.back()">Go Back</button>
