---
layout: post
title:  "Azure Sentinel Lab"
date:   2024-04-20
author: C. Casquatch
comments: false
tag: ['azure', 'SIEM', 'sentinel', 'SOC']
---

## Description
> Project includes the setup Azure Sentinel (SIEM) and its' connection to a live virtual machine, acting as a honey pot.
> I observed live attacks (RDP Brute Force) from all around the world. I used a custom PowerShell script (provided by Josh Madakor) to look up the attackers Geolocation information and plot it on the Azure Sentinel Map.
> Also thanks to Nirakar Sapkota for the query script.

#### Languages and Utilities Used
* PowerShell
* Azure Sentinel

#### Environments Used
* Windows 10
* Microsoft Azure

#### Program walk-through:
* * *
##### In Sentinel; creating our honeypot VM. Below are the configuration settings we will be using
# photo1

##### In Sentinel; Creating our Log Analytics work space
# photo2

##### In Sentinel; Connecting the VM to the Log Analytics work space
# photo3

##### Remoting from our desktop into our VM and providing confiurations
# photo4

##### In Sentinel; Creating custom logs for our analytics work space
# photo5

##### In Sentinel; Generating a query to extract custom fields from the existing raw data field
# photo6

##### In Sentinel; Running the query from the last screenshot
# photo7

##### In Sentinel; Creating a workbook to provide a visualisation of the honeypot attacks
# photo8

##### In Sentinel; Map of attacks on our honeypot VM
# photo9

