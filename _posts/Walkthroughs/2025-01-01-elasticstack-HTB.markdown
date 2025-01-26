---
layout: post
title:  "CSecurity Monitoring & SIEM Fundamentals Module: Intro to Elastic Stack"
date: 2025-01-01
author: C. Casquatch
comments: false
tag: ['elastic', 'siem', 'htb']
---

> Walkthrough: Introduction to the Elastic Stack


Introduction to the Elastic Stack
=================================

The Elastic Stack, often referred to as the ELK Stack (Elasticsearch, Logstash, and Kibana), is a powerful suite of tools for collecting, searching, and visualising log data. This walkthrough outlines the essential concepts and steps in the module, with placeholders for screenshots to be added later.

The high-level architecture of the Elastic stack can be enhanced in resource-intensive environments with the addition of Kafka, RabbitMQ, and Redis for buffering and resiliency, and nginx for security.

Components of the Elastic Stack
-------------------------------

### Elasticsearch

Elasticsearch is a distributed and JSON-based search engine, designed with RESTful APIs. As the core component of the Elastic stack, it handles indexing, storing, and querying. Elasticsearch empowers users to conduct sophisticated queries and perform analytics operations on the log file records processed by Logstash.

### Logstash

Logstash is responsible for collecting, transforming, and transporting log file records. Its strength lies in its ability to consolidate data from various sources and normalize them. Logstash operates in three main areas:

*   **Process input:** Logstash ingests log file records from remote locations, converting them into a format that machines can understand. It can receive records through different input methods, such as reading from a flat file, a TCP socket, or directly from syslog messages. After processing the input, Logstash proceeds to the next function.
*   **Transform and enrich log records:** Logstash offers numerous ways to modify a log record's format and content. Specifically, filter plugins can perform intermediary processing on an event, often based on a predefined condition. Once a log record is transformed, Logstash processes it further.
*   **Send log records to Elasticsearch:** Logstash utilizes output plugins to transmit log records to Elasticsearch.

### Kibana

Kibana serves as the visualization tool for Elasticsearch documents. Users can view the data stored in Elasticsearch and execute queries through Kibana. Additionally, Kibana simplifies the comprehension of query results using tables, charts, and custom dashboards.

### Beats

Beats is an additional component of the Elastic stack. These lightweight, single-purpose data shippers are designed to be installed on remote machines to forward logs and metrics to either Logstash or Elasticsearch directly. Beats simplify the process of collecting data from various sources and ensure that the Elastic Stack receives the necessary information for analysis and visualization.

        Beats -> Logstash -> Elasticsearch -> Kibana
        Beats -> Logstash
        Beats -> Elasticsearch -> Kibana
        Beats -> Elasticsearch
    

The Elastic Stack As A SIEM Solution
------------------------------------

The Elastic stack can be used as a Security Information and Event Management (SIEM) solution to collect, store, analyze, and visualize security-related data from various sources.

To implement the Elastic stack as a SIEM solution, security-related data from various sources such as firewalls, IDS/IPS, and endpoints should be ingested into the Elastic stack using Logstash. Elasticsearch should be configured to store and index the security data, and Kibana should be used to create custom dashboards and visualizations to provide insights into security-related events.

To detect security-related incidents, Elasticsearch can be used to perform searches and correlations on the collected security data.

### Kibana Query Language (KQL)

KQL is a powerful and user-friendly query language designed specifically for searching and analyzing data in Kibana. It simplifies the process of extracting insights from your indexed Elasticsearch data, offering a more intuitive approach than Elasticsearch's Query DSL. Let's explore the technical aspects and key components of the KQL language:

*   **Basic Structure:** KQL queries are composed of field:value pairs, with the field representing the data's attribute and the value representing the data you're searching for. For example:

event.code:4625

The KQL query `event.code:4625` filters data in Kibana to show events that have the Windows event code 4625. This Windows event code is associated with failed login attempts in a Windows operating system.

### Further Features of KQL

KQL supports:

*   **Free Text Search:** For instance, searching for `"svc-sql1"`.
*   **Logical Operators:** AND, OR, and NOT for complex queries.
*   **Comparison Operators:** Such as `:`, `:>`, and `:<`.
*   **Wildcards and Regular Expressions:** For example, `event.code:4625 AND user.name: admin*`.

### How to Identify Available Data

Utilize tools like the Discover feature in Kibana and Elastic's comprehensive documentation to identify and explore fields and values.

### The Elastic Common Schema (ECS)

The Elastic Common Schema (ECS) provides a shared and extensible vocabulary for events and logs across the Elastic Stack. Using ECS fields offers numerous advantages, including:

*   Unified data view.
*   Improved search efficiency.
*   Enhanced correlation across data sources.
*   Better visualizations.
*   Interoperability with Elastic solutions.
*   Future-proofing for Elastic ecosystem advancements.

By leveraging ECS, organizations can streamline their operations and make the most of the Elastic Stack's capabilities.


### Questions:

#### Navigate to http://[Target IP]:5601, click on the side navigation toggle, and click on "Discover". Then, click on the calendar icon, specify "last 15 years", and click on "Apply". Finally, choose the "windows*" index pattern. Now, execute the KQL query that is mentioned in the "Comparison Operators" part of this section and enter the username of the disabled account as your answer. Just the username; no need to account for the domain.
ANSWER:

#### Now, execute the KQL query that is mentioned in the "Wildcards and Regular Expressions" part of this section and enter the number of returned results (hits) as your answer.
ASNWER: 


<button onclick="history.back()">Go Back</button>
