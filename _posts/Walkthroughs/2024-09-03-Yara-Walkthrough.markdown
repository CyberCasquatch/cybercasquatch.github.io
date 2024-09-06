---
layout: post
title:  "Yara Room Walkthrough (THM)"
date: 2024-04-21
author: C. Casquatch
comments: false
tag: ['Yara', 'Digital Forensics', 'Threat Hunting']
---

> YARA is a tool used to identify and classify malware by creating rules that describe patterns found in malicious files. > It helps cybersecurity professionals detect and analyze malware by matching these patterns against a set of predefined
> rules.


### Task 1: Introduction
After reading the intro we can hit the Question Done button.

### Task 2: What is Yara?
Yara is a language and application used for threat intelligence, forensics, and threat hunting. It can identify information based on binary and textual patterns, such as hexadecimal and strings contained in a file.
To answer the questions in this task, everything we need will be in the written section of the task.

#### Questions:
**What is the name of the base-16 numbering system that Yara can detect?**
_Answer: Hexadecimal_
Would the test “Enter your Name” be a string in an application? (Yay/Nay)
_Answer: Yay_

### Task 3: Deploy
In this task we will deploy the machine and start our attack box.
To do this:

![photo1](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture1.png)

Start your attack box – whether you connect to a VPN or use split view.
I usually use split view – so that is what I will be using for this walkthrough.
Also make note of the credentials given to us in this task.

![photo2](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture2.png)

These will assist us in further tasks.
Click the Question Done button and let’s move onto task 4.

### Task 4: Introduction to Yara Rules
Read through this section and practice making your first rule. Once finished, click the question done button and move onto the next task.

### Task 5: Expanding on Yara Rules
In this task there are two links that I encourage you to save into bookmarks for future use.
One is at the very top of the read –
[Writing YARA rules — yara 4.4.0 documentation](https://yara.readthedocs.io/en/stable/writingrules.html)

The other is at the bottom –
[Security Infographics. I often do infographics to share… | by Thomas Roccia | SecurityBreak](https://blog.securitybreak.io/security-infographics-9c4d3bd891ef#18dd)
Both give further explanations of how Yara operates.

Once you have finished reading this section, click on the question done button and move onto the next task.

### Task 6: Yara Modules
Read through this task and click on the question done button.

### Task 7: Other tools and Yara
This room introduces a few tools that we will be using in further tasks of this room to complete it.
The main one being LOKI by Florian Roth.

I encourage you to save any links here for future use.
* [Loki/README.md at master · Neo23x0/Loki (github.com)](https://github.com/Neo23x0/Loki/blob/master/README.md)
* [Releases · Neo23x0/Loki (github.com)](https://github.com/Neo23x0/Loki/releases)
* [THOR Lite - Nextron Systems (nextron-systems.com)](https://www.nextron-systems.com/thor-lite/)
* [Neo23x0/Fenrir: Simple Bash IOC Scanner (github.com)](https://github.com/Neo23x0/Fenrir)
* [Introducing “YAYA”](https://www.eff.org/deeplinks/2020/09/introducing-yaya-new-threat-hunting-tool-eff-threat-lab)

### Task 8: Using LOKI and its Yara rule set
Read through the section and get familiar with where Loki can be found on the system along with the ‘suspicious-files’ location.

The scenario we have been given:
We are the security analyst for a mid-size law firm. A co-worker discovered suspicious files on a web server within our organisation. These files were discovered while performing updates to the corporate website. The files have been copied to your machine for analysis. The files are located in the ‘suspicious-files’ directory. Use Loki to answer the questions.

#### Questions:
**Scan file 1. Does Loki detect this file as suspicious/malicious or benign?**
Let’s instruct Loki to scan the suspicious file1. Cd into the directory and scan the file:

![photo3](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture3.png)

If we scroll down the scan, we can see our answer to this question.

![photo4](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture4.png)

_Answer: Suspicious_

**What Yara rule did it match on?**

For this question, we’ll focus on the word ‘match’ and have a look in the scan.

![photos5](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture5.png)
 
_Answer: webshell_metaslsoft_

**What does Loki classify this file as?**
If we use THM’s Hint button we can see they are asking us to look at the description which lives under the MATCH: we just discovered.

_Answer: Web Shell_

**Based on the output, what string within the Yara rule did it match on?**
If we look below the description we can see the MATCHES:
This indicates the string we are looking for –

![photo6](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture6.png)

_Answer: Str1_

**What is the name and version of this hack tool?**

Take the ‘hint’ advice and look under the FIRST_BYTES line in our output. We will need the information after the * symbol.

_Answer: b374k 2.2_

**Inspect the actual Yara file that flagged file 1. Within this rule, how many strings are there to flag this file?**

As per Haircutfish on Medium [TryHackMe Yara Room. Learn the applications and language… | by Haircutfish | Medium](https://medium.com/@haircutfish/tryhackme-yara-room-d279ccb5cbb3) – we will use nano to look into this file and search for the Yara rule that it matched on within the file.

We can do this by going into the signature-base directory of the Loki file on our Yara system and using the command:
nano thor-webshells.yar
you can use Haircutfish’s shortcut “nano /home/cmnatic/tools/Loki/signature-base/yara/thor-webshells.yar” to get the same results.
Following nano rules – we hit F6 to enable search and input the Yara rule it matched on, which is webshell_metaslsoft

![photo7](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture7.png)

Here we can see there is only one string.
_Answer: 1_

**Scan file 2. Does Loki detect this file as suspicious/malicious or benign?**

If we run through the loki scanner again and use file2 as our directory. To do this we can use the command we used for the first question - python ../../tools/Loki/loki.py -p .
Once scanned we can see our answer.
_Answer: Benign_

**Inspect file 2. What is the name of the version of this web shell?**
We can use nano here again while in the file2 directory.
If we ls in this directory we can see the file 1ndex.php
We can use the command nano 1ndex.php and look at the file.

![photo8](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture8.png)

Our answer is at the top of the file.
_Answer: b374k 3.2.3_

### Task 9: Creating Yara rules with yarGen
Use the command in the teachings to generate the yar file (be sure to be in the yarGen directory by going through the tools folder (cd ~/tools/yarGen))
The command we’ll use to generate the file is:
python3 yarGen.py -m /home/cmnatic/suspicious-files/file2 --excludegood -o /home/cmnatic/suspicious-files/file2.yar
**From within the root of the suspicious files directory, what command would you run to test the Yara and you Yara rule against file2?**
Looking back through task 4, the rule file we just created, and the hint we are given:
_Answer: yara file2.yar file2/1ndex.php_

**Did Yara rule flag file 2? (Yay/Nay)**
First we have to cd back into the correct directory (file2) to launch the rule.

![photo9](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture9.png)

If we think back to task 4 – this means that the Yara rule flagged file 2.
_Answer: Yay_

Now copy the Yara rule you created into the loki signatures directory.
Within the file2 directory use the command:
mv /home/cmnatic/suspicious-files/file2.yar /home/cmnatic/tools/Loki/signature-base/yara/
This was given to us when we first scanned the file in loki – we use the mv command and the file we want to move into the file location we want to move it to.
Now we can activate loki and scan the file with python like we did before.
Syntax - python ../../tools/Loki/loki.py -p .

**Test the Yara rule with Loki, does it flag file 2?**
 Loki has brought the goods this time.

![photo10](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture10.png)

_Answer: Yay._

**What is the name of the variable for the string that it matched on?**
Our hint from THM says look at $x1…

![photo11](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture11.png)

Loki has a string match here with a variable of Zepto
_Answer: Zepto_

**Inspect the Yara rule, how many strings were generated?**
If we go to the loki folder and cat the file –

![photo12](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture12.png)

There are 20 strings here.
_Answer: 20_

**One of the conditions to match on the Yara rule specifies file size. The file has to be less than what amount?**
At the top of the screen shot above is our answer.
_Answer: 700KB_

### Task 10: Valhalla
**Enter the SHA256 hash file into Valhalla. Is this file attributed to an APT group?**

![photo13](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture13.png)

_Answer: Yay._

**Do the same for file 2. What is the name of the first Yara rule to detect file 2?**
 
![photo14](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture14.png)

_Answer: Webshell_b374_rule1_

**Examine the information for file 2 from Virus Total (VT). The Yara Signature Match is from what scanner?**
We’re given the first hint which is VT – so we click that on the Valhalla website.

![photos15](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture15.png)

The second hint tells us that it’s on the community tab.
When the VT page loads – I am given the match and the type of scanner.

![photo16](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture16.png)

_Answer: THOR APT Scanner_

**Enter the SHA 256 hash of file 2 into VT. Did every AV detect this as malicious? (Yay/Nay)**
Once searched, we can see that not every AV did.

![photo17](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture17.png) 

_Answer: Nay_

**Besides .PHP, what other extension is recorded for this file?**

Our hint is to look under the details tab in VT to find out the extensions.

![photo18](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture18.png)

After clicking the down arrow at the bottom of the names we have all the types of file extensions that it could use. .txt, .php, .html, .exe, .sys, .php5
THM says it’s only 3 characters, so try each three letter extension to see which one works out for us.
In this case – it’s .exe
_Answer: EXE_

**What Javascript library is used by file 2?**
Since we are done with VT – we will head back to the Valhalla page and click on the link icon which will take us to where our hint is telling us (Github)

From here we will navigate to the index.php file and search within it. To do this we will CTRL F and search for Javascript or .js

![photo19](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture19.png)


One word here looks awfully familiar – so I will start with that.
And lo and behold, that is our answer here.
_Answer: Zepto_

**Is this Yara rule in the default Yara file Loki uses to detect these types of hack tools? (Yay/Nay)**
In the yara terminal we can grep the rule we want with the following command:

![photo20](https://github.com/CyberCasquatch/cybercasquatch.github.io/blob/main/assets/images/yara/yaraPicture20.png)

Nothing shows up here.
_Answer: Nay_

#### Task 11: Conclusion
This one is self-explanatory – read through the exit and click the Question Done button. From here we can move onto the next room.

<button onclick="history.back()">Go Back</button>
