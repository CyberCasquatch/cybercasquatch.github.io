---
layout: post
title:  "Introduction to Splunk and SPL"
date: 2025-02-4
author: C. Casquatch
comments: false
tag: ['spl', 'splunk', 'htb']
---

> Splunk's Search Processing Language (SPL) is the backbone of data analysis within Splunk, enabling users to search, correlate, and visualize vast amounts of data efficiently. In this guide, we'll walk through the fundamentals of SPL, practical search examples, and techniques for identifying available data and fields within your Splunk environment.

## Introduction to SPL

SPL is designed to process large datasets quickly, allowing analysts to extract meaningful insights from raw data. Its syntax resembles SQL but is tailored for event-based data, making it indispensable for security analysts, IT administrators, and data engineers alike.

## Basic SPL Search Commands

### 1. **Simple Keyword Search**
```spl
error
```
This command searches for the keyword "error" across all indexed data.

### 2. **Specifying an Index**
```spl
index=main error
```
Limits the search to the `main` index, improving performance.

### 3. **Time Range Filtering**
```spl
index=main earliest=-24h latest=now error
```
Filters events from the last 24 hours.

### 4. **Field Filtering**
```spl
index=main status=404
```
Finds events where the `status` field equals 404.

## Data Transformation Commands

### 1. **Table Command**
```spl
index=main | table _time, host, source, sourcetype
```
Displays search results in a table format with specified fields.

### 2. **Stats Command**
```spl
index=main | stats count by host
```
Counts events grouped by the `host` field.

### 3. **Top Command**
```spl
index=main | top source
```
Displays the most common `source` values.

### 4. **Rare Command**
```spl
index=main | rare source
```
Identifies the least common `source` values, useful for anomaly detection.

## Advanced SPL Techniques

### 1. **Identifying Rare Parent Images**
```spl
index=main | rare limit=20 useother=f ParentImage
```
Displays the 20 least common values of the `ParentImage` field, potentially highlighting suspicious processes.

### 2. **Field Summary for Data Exploration**
```spl
index=* sourcetype=* | fieldsummary | where count < 100 | table field, count, distinct_count
```
Provides a summary of fields with less than 100 occurrences, helping identify unusual data patterns.

### 3. **Event Distribution Over Time**
```spl
index=* sourcetype=* | bucket _time span=1d | stats count by _time, index, sourcetype | sort -_time
```
Visualizes event distribution daily, aiding in trend analysis.

## Identifying Available Data

### Approach 1: Using SPL

#### **List All Indexes**
```spl
| eventcount summarize=false index=* | table index
```
Shows all indexes with event counts.

#### **List All Source Types**
```spl
| metadata type=sourcetypes | table sourcetype
```
Lists all sourcetypes in the environment.

#### **Explore Data in a Specific Sourcetype**
```spl
sourcetype="WinEventLog:Security" | table _raw
```
Displays raw event data for the `WinEventLog:Security` sourcetype.

#### **Identify Available Fields**
```spl
sourcetype="WinEventLog:Security" | fieldsummary
```
Summarizes all fields present in the specified sourcetype.

### Approach 2: Using Splunk's UI

1. **Data Sources:** Navigate to **Settings > Data Inputs** to view data ingestion sources.
2. **Events:** Use the **Search & Reporting** app to explore indexed events.
3. **Fields:** Click on any event to view **Selected Fields** and **Interesting Fields**.
4. **Data Models:** Go to **Settings > Data Models** to explore structured data models and their fields.

## Practical Tips for Effective SPL Searches

- **Use Filters Early:** Narrow down data with specific indexes, sourcetypes, or time ranges.
- **Leverage `rare` and `top`:** Quickly identify anomalies and common patterns.
- **Summarize with `stats`:** Aggregate data for insights without overwhelming detail.
- **Practice Makes Perfect:** SPL proficiency comes with regular use and experimentation.

## Additional Resources

- [Splunk Search Reference Guide](https://docs.splunk.com/Documentation/SCS/current/SearchReference/Introduction)
- [Splunk Cloud Search Reference](https://docs.splunk.com/Documentation/SplunkCloud/latest/SearchReference/)
- [Splunk Documentation](https://docs.splunk.com/Documentation/SplunkCloud/latest/Search/)


### Questions

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through an SPL search against all data the account name with the highest amount of Kerberos authentication ticket requests. Enter it as your answer.
ANSWER:

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through an SPL search against all 4624 events the count of distinct computers accessed by the account name SYSTEM. Enter it as your answer.
ANSWER:

#### Navigate to http://[Target IP]:8000, open the "Search & Reporting" application, and find through an SPL search against all 4624 events the account name that made the most login attempts within a span of 10 minutes. Enter it as your answer.
ANSWER:





<button onclick="history.back()">Go Back</button>
