---
layout: post
title:  "TidBit 10"
date:   2024-09-03
author: C. Casquatch
comments: false
categories: cyber security
---

# TidBit 10, taken from weekly writing 10

I'm doing another TryHackMe room for practice. 
The room I'm completing is Crack That Hash. Which is just a hands-on-learning room to crack hashes as practice. 
 
Crack the Hash [Walkthrough](https://www.youtube.com/watch?v=FtA3ntx_wkQ)
I found this guy who had a really short video with a walkthrough about how to do it. I thought it was a scam. But they're actually a genius. 
They use Search-That-Hash and Hashcat to complete the room. I was amazed. I love Search-That-Hash - what a concept. Below is the 'Level 1'
 
I created a .txt file with the listed hashes to crack. Just a simple list in the .txt file. 
The syntax for Search The Hash is > sth -f NAME_OF_FILE.txt
For the second level - STH could not crack all the hashes because of the salt. 
 
TBH the last one stumped me because what worked in the video did not work for me. And I was lucky because my STH worked for the fourth-level-two hash, so I didn't have to use the 1800 hash-mode or extra syntax to find it, though I could if I neede too. 
Our hint for the last has on level two is HMAC-SHA1.
I chose 160 because of the SALT. From example_hashes [hashcat wiki](https://hashcat.net/wiki/doku.php?id=example_hashes)
 
(Note: I kept my file name the same because I was lazy and did not want to create another file or change the name) -- my syntax was: hashcat -m 160 -a 0 lvl1.txt /FILE_PATH_TO_ROCKYOU.TXT/rockyou.txt
Then I got it with the output. Ye. 
 
[back](./TidBitMain.html)
