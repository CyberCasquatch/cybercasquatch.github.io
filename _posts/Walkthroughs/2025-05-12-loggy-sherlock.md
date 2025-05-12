---
layout: post
title:  "Sherlock Loggy: Very Easy"
date:   2025-05-12
author: C. Casquatch
comments: false
tag: ['very easy', 'Loggy', 'sherlock', 'HTB', 'malware analysis']
---

> From HTB website, our scenario "Janice from accounting is beside herself! She was contacted by the SOC to tell her that her work credentials were found on the dark web by the threat intel team. 
> We managed to recover some files from her machine and sent them to the our REM analyst."


### Environments and Utilities Used
* Parrot OS
* Terminal
* Ghidra
* VirusTotal

_Make sure to download the Loggy.zip to a safe place to analyse._

![Before Start](/assets/images/Loggy/beforestart.JPG)

* I didn't finish this Sherlock - the credential question was a little odd. I looked in every place I could think of and even checked a few other write-ups to make sure i wasn't missing anything. 
* I found the answer in the other write-ups but I couldn't find it when I looked in the same place on my own hardware so I haven't submitted the answer.

## Tasks


#### What is the SHA-256 hash of this malware binary?
For this task we can use the terminal and
```
sha256sum 'Loggy.exe'
```

![Task 1](/assets/images/Loggy/task1.JPG)

#### What programming language (and version) is this malware written in?

There are several ways to obtain this answer: one of them is to throw the hash in virustotal and look through the details page to get the answer.
![Task 2](/assets/images/Loggy/task2.JPG)

Another way to find it is to look at outputs from Ghidra.
![Task 2](/assets/images/Loggy/ghidra.JPG)

Another way is to use grep eg:
```
strings loggy.exe | grep -i "go1"
```
![Task 2](/assets/images/Loggy/task2B.JPG)

#### There are multiple GitHub repos referenced in the static strings. Which GitHub repo would be most likely suggest the ability of this malware to exfiltrate data?
For this task we can try different grep methods:
```
strings Loggy.exe | grep -E "exfil|upload|s3|ftp|dropbox|telegram"
```
![Task 3](/assets/images/Loggy/task3a.JPG)
Or:
```
strings Loggy.exe | grep -Eo "exfil|upload|s3|ftp|dropbox|telegram"
```
![Task 3](/assets/images/Loggy/task3b.JPG)


#### What dependency, expressed as a GitHub repo, supports Janice’s assertion that she thought she downloaded something that can just take screenshots?
For this task we want to look for anything related to github and screenshots:

```
strings Loggy.exe | grep -iE "screenshot|screen|capture|spy|desktop"
```
![Task 4](/assets/images/Loggy/task4a.JPG)


#### Which function call suggests that the malware produces a file after execution?

Ok a little bit of research here first if we don't know how Go works. The function most associated with producing a file would probably be 'write.file' or 'os.create'
![Task 5](/assets/images/Loggy/task5a.JPG)

To make sure we are correct in this assumption based on our findings about go, we need to search our .exe file to see if this shows up and in what context.
Let's search for os.creat and write.file:
```
strings Loggy.exe | grep -iE "os.Create|WriteFile|OpenFile|NewWritier|gob.encode"
```
![Task 5](/assets/images/Loggy/task5b.JPG)

![Task 5](/assets/images/Loggy/task5c.JPG)

Based on the output we can tell which one produces a file after execution.


#### You observe that the malware is exfiltrating data over FTP. What is the domain it is exfiltrating data to?
This one took me a hot second to realise that it was staring at me, I think I had to circle back because I couldn't see it at first.
I started off searching for ip addresses but this was the wrong approach.
The command I used in the end:
```
strings Loggy.exe | grep -i "addressname"
```
![Task 6](/assets/images/Loggy/task6.JPG)

#### What are the threat actor’s credentials?
:( I have looked in the same place of the file that other users have reported that they found the credentials but I could not find them in the same place.
This screenshot shows the place in the malware that these credentials were found for other users but in my case I couldn't see them.
![Task 7](/assets/images/Loggy/task7a.JPG)


#### What file keeps getting written to disk?
This one is fairly simple. If we look in the Loggy file where our screenshots and other .txt files are, we can see the answer. 
![Task 8](/assets/images/Loggy/task8.JPG)

#### When Janice changed her password, this was captured in a file. What is Janice's username and password?
For this task we need to open the keylog.txt file to obtain the answer.
![Task 9](/assets/images/Loggy/task9.JPG)

#### What app did Janice have open the last time she ran the "screenshot app"?
Have a look through the screenshots that are saved in this Loggy file - our answer is there.
![Task 10](/assets/images/Loggy/task10.JPG)

* * *

<button onclick="history.back()">Go Back</button>
