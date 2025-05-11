---
layout: post
title:  "Sherlock Origins: Very Easy"
date:   2025-05-11
author: C. Casquatch
comments: false
tag: ['very easy', 'Origins', 'sherlock', 'HTB']
---

> From HTB website, our scenario "A major incident has recently occurred at Forela. Approximately 20 GB of data were stolen from internal s3 buckets and the attackers are now extorting Forela.
> During the root cause analysis, an FTP server was suspected to be the source of the attack.
> It was found that this server was also compromised and some data was stolen, leading to further compromises throughout the environment.
> You are provided with a minimal PCAP file. Your goal is to find evidence of brute force and data exfiltration."


### Environments and Utilities Used
* Parrot OS
* Wireshark

_Make sure to download the Origins.zip to a safe place to analyse._


## Tasks


#### What is the attacker's IP address?
- To start this process I looked for traffic just before the FTP traffic that is being mentioned in the scenario as the root cause for the attack. The IP address we are looking for is in this data.
![Task 1](/assets/images/Origins/task1.JPG)

#### It's critical to get more knowledge about the attackers, even if it's low fidelity. Using the geolocation data of the IP address used by the attackers, what city do they belong to?
- Go to whichever iplookup site you prefer and put the attacker's IP address in the search to get the city it belongs to.
![Task 2](/assets/images/Origins/task2.JPG)


#### Which FTP application was used by the backup server? Enter the full name and version. (Format: Name Version)
- The word I latched onto here was 'version'. When i was looking for this answer, 'vs' was a fair indicator that it was the version we were looking for, and it's common here.
![Task 3](/assets/images/Origins/task3.JPG)

#### The attacker has started a brute force attack on the server. When did this attack start?
- I'm surprised I didn't need a filter for this one. But if you need a filter, we could probably filter on the 'info' and look for 'pass' or 'password' to find this answer.
- Look for the first session of passwords being bruteforced and this will give you the time stamp.
![Task 4](/assets/images/Origins/task4.JPG)


#### What are the correct credentials that gave the attacker access? (Format username:password)
- After finding the start of the attack, I searched until I found where logins were taking place. Look for 'Login Successful' to figure out which credentials were correct.
![Task 5](/assets/images/Origins/task5a.JPG)
![Task 5](/assets/images/Origins/task5b.JPG)
![Task 5](/assets/images/Origins/task5c.JPG)

#### The attacker has exfiltrated files from the server. What is the FTP command used to download the remote files?
- I used the hint for this one. We can filter for 'ftp-data' and find the answer. 

#### Attackers were able to compromise the credentials of a backup SSH server. What is the password for this SSH server?
- I used the hint for this one as well. We can go to File-> Export Objects-> FTP-Data. The files here may contain the info we need.
![Task 7](/assets/images/Origins/task7.JPG)
![Task 7](/assets/images/Origins/task7c.JPG)

#### What is the s3 bucket URL for the data archive from 2023?
- For this task we only need to open the .txt file that we found in task 7. 
![Task 8](/assets/images/Origins/task8.JPG)

#### The scope of the incident is huge as Forela's s3 buckets were also compromised and several GB of data were stolen and leaked.
#### It was also discovered that the attackers used social engineering to gain access to sensitive data and extort it. 
#### What is the internal email address used by the attacker in the phishing email to gain access to sensitive data stored on s3 buckets?
- This answer can be found in the same .txt file we used for task 8.

* * *

<button onclick="history.back()">Go Back</button>
