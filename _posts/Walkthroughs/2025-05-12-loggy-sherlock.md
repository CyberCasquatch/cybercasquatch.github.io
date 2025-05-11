---
layout: post
title:  "Sherlock Loggy: Very Easy"
date:   2025-05-12
author: C. Casquatch
comments: false
tag: ['very easy', 'Origins', 'sherlock', 'HTB', 'malware analysis']
---

> From HTB website, our scenario "Janice from accounting is beside herself! She was contacted by the SOC to tell her that her work credentials were found on the dark web by the threat intel team. 
> We managed to recover some files from her machine and sent them to the our REM analyst."


### Environments and Utilities Used
* Parrot OS
* Terminal
* Ghidra

_Make sure to download the Loggy.zip to a safe place to analyse._

![Before Start](/assets/images/Loggy/beforestart.JPG)

* I didn't finish this one - the credential question was a little odd. I looked in all the right places and even checked a few other write-ups to make sure i wasn't missing anything. 
* I found the answer in the other write-ups but I couldn't find it when I looked in the same place on my own hardware so I haven't submitted the answer.

## Tasks


#### What is the SHA-256 hash of this malware binary?

![Task 1](/assets/images/Loggy/task1.JPG)

#### What programming language (and version) is this malware written in?

![Task 2](/assets/images/Loggy/task2.JPG)


#### There are multiple GitHub repos referenced in the static strings. Which GitHub repo would be most likely suggest the ability of this malware to exfiltrate data?

![Task 3](/assets/images/Loggy/task3.JPG)

#### What dependency, expressed as a GitHub repo, supports Janice’s assertion that she thought she downloaded something that can just take screenshots?

![Task 4](/assets/images/Loggy/task4.JPG)


#### Which function call suggests that the malware produces a file after execution?

![Task 5](/assets/images/Loggy/task5a.JPG)
![Task 5](/assets/images/Loggy/task5b.JPG)
![Task 5](/assets/images/Loggy/task5c.JPG)

#### You observe that the malware is exfiltrating data over FTP. What is the domain it is exfiltrating data to?
![Task 6](/assets/images/Loggy/task6.JPG)

#### What are the threat actor’s credentials?
:( 

#### What file keeps getting written to disk?

![Task 8](/assets/images/Loggy/task8.JPG)

#### When Janice changed her password, this was captured in a file. What is Janice's username and password?
![Task 9](/assets/images/Loggy/task9.JPG)

#### What app did Janice have open the last time she ran the "screenshot app"?

![Task 10](/assets/images/Loggy/task10.JPG)

* * *

<button onclick="history.back()">Go Back</button>
