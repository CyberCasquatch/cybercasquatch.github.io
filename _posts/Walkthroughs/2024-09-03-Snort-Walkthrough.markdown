---
layout: post
title:  "Snort Room Walkthrough"
date:   2024-09-03
author: C. Casquatch
comments: false
categories: Walkthroughs
---

### Task1
Read through task and complete the question.

### Task 2
In Task 2 click ‘Start Machine’ and complete the question.

### Task 3
While the VM is loading – read through Task 3 and complete the questions. All answers can be found throughout the reading in Task 3.

### Task 4
Read through Task 4 and start exploring snort.
Use the hints with the questions when needed.

**Question1:
Run the Snort instance and check the build number.**
 
 # photo1
The build number is 149. 

**Question 2:
Test the current instance with "/etc/snort/snort.conf" file and check how many rules are loaded with the current build.**
 
 # photo2
If we scroll up through the file we can see that there are 4151 rules loaded.

**Question 3:
Test the current instance with "/etc/snort/snortv2.conf" file and check how many rules are loaded with the current build.**

 # photo3
If we run with the snortv2 file we can see that 1 rule has loaded.

### Task 5
Read through task 5 and practice using parameter combinations.

### Task 6
Read through the documentation on Task 6.
We will be using
```
 sudo snort -dev -K ASCII -l .
```
 to investigate the traffic.
 
Execute the traffic generator script and choose "TASK-6 Exercise". Wait until the traffic ends, then stop the Snort instance. Now analyse the output summary and answer the question.
Remember to generate traffic we use

```
sudo ./traffic-generator.sh
```

**Question 1: 
Now, you should have the logs in the current directory. Navigate to folder "145.254.160.237". What is the source port used to connect port 53?**
 
 # photo4
Here we can see the source port is 3009.

**Question 2:
Use snort.log.1640048004 
Read the snort.log file with Snort; what is the IP ID of the 10th packet?**

Command:
```
Snort -r snort.log.1640048004 -n 10
```
Also make sure you are in the correct directory.

# photo5
The answer would be 49313.

**Question 3:
Read the "snort.log.1640048004" file with Snort; what is the referer of the 4th packet?**
 
 # photo6
Look for the word ‘referer’ and we have our answer.

**Question 4:
Read the "snort.log.1640048004" file with Snort; what is the Ack number of the 8th packet?**
 
 # photo7
TCP port 80 number is 41.

### Task 7
_Read through task 7 and follow along with their examples to get the hang of some syntax.
At the end we will investigate the traffic with the default config file with the following syntax._

```
sudo snort - /etc/snort/snort.conf -A full -l .
```

_Execute the traffic generator script and choose "TASK-7 Exercise". Wait until the traffic stops, then stop the Snort instance. Now analyse the output summary and answer the question._
Use the traffic generator syntax

```
sudo ./traffic-generator.sh
```

**Question 1:
What is the number of the detected HTTP GET methods?**

Use the hints when needed.
 
 # photo8
The question specifies ‘GET’ methods and here under HTTP we can see there are 2.
Here we are encouraged to practice more parameters by using the traffic=generator script.

### Task 8
_Read and follow along with the documentation for task 8._
For the questions make sure you are in the correct directory – for now we want to be in TASK-8 folder.
Investigate the mx-1.pcap file with the default configuration file.

```
sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-1.pcap
```

**Question 1: What is the number of the generated alerts?**

 # photo9
Next to ‘alerts’ is says 170.

**Question 2:
Keep reading the output. How many TCP Segments are Queued?**

 # photo10
As we scroll through we can see that there are 18 TCP segments that are queued.

**Question 3:
Keep reading the output. How many "HTTP response headers" were extracted?**

 # photo11
We scroll a little more and find that there were 3 HTTP response headers extracted.
For the next question
Investigate the mx-1.pcap file with the second configuration file.

```
sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-2.pcap
```

**Question 4: what is the number of the generated alerts?**
 
 # photo12
68.

#### For the next two questions
_Investigate the mx-2.pcap file with the default configuration file._

```
sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-2.pcap
```

**Question 5: What is the number of the generated alerts?**

 # photo13
Alerts for this one are at 340.

**Question 6: Keep reading the output. What is the number of the detected TCP packets?
Use the hint for question 5: Check for the TCP Port Filter.**

# photo14 
82.

#### For this question
_Investigate the mx-2.pcap and mx-3.pcap files with the default configuration file._

```
sudo snort -c /etc/snort/snort.conf -A full -l . --pcap-list="mx-2.pcap mx-3.pcap"
```

**Question 7: What is the number of the generated alerts?**

 # photo 15
1020.

### Task 9
_Read through task 9._
#### For these questions use “task9.pcap”.
_Also make sure that you have the ‘local.rules’ file open to write down all the rules you are going to run. Found in the task 9 folder._

**Question 1:
Write a rule to filter IP ID "35369" and run it against the given pcap file. What is the request name of the detected packet?**

```
snort -c local.rules -A full -l . -r task9.pcap
```

I suggest using the hint: Try to filter different protocols like TCP/UDP/ICMP. id:35369;

 # photo16
TIMESTAMP REQUEST

**Question 2: Create a rule to filter packets with Syn flag and run it against the given pcap file. What is the number of detected packets?**

 # photo17

Detected packets are 1

**Question 3:
Clear the previous log and alarm files and deactivate/comment out the old rule.
Write a rule to filter packets with Push-Ack flags and run it against the given pcap file. What is the number of detected packets?**

 # photo18
216.

**Question 4:
Clear the previous log and alarm files and deactivate/comment out the old rule.
Create a rule to filter packets with the same source and destination IP and run it against the given pcap file. What is the number of packets that show the same source and destination address?**

 # photo19
I initially got 10 – but if you cat on the alerts. You can see which ones have our rule.

 # photo 20
I counted 7.

**Question 5: Case Example - An analyst modified an existing rule successfully. Which rule option must the analyst change after the implementation?**

Revs must change.

