---
layout: post
title:  "CI/CD Pipeline Room Walkthrough"
date:   2024-04-16
author: C. Casquatch
comments: false
tag: ['Gitlab', 'THM', 'CI/CD', 'pipeline']
---

> This project consists of practicing the following: Explore what it takes to secure a DevOps pipeline and the builds it produces.
> Understanding the potential risks and consequences of insecure build processes.
> Explore common insecurities and how threat actors can exploit these to compromise not only the process, but also production systems.

![PL](/assets/images/CICD/PipelineLieutenant.PNG)

~ This room took me a while to get through. If I got stuck, I headed to [Tyler Ramsbey](https://www.youtube.com/@TylerRamsbey) - He has two great videos to assist with the room.

### Languages and Utilities Used
* Gitlab
* Bash

### Environments Used
* Gitlab
* Kali Linux

# Room walk-through

#### Learning Objectives for this room:
* Understand the significance of CI/CD and build system security within the DevSecOps pipeline.
* Explore the potential risks and consequences associated with insecure CI/CD pipelines and build processes.
* Gain awareness of the importance of integrating robust security measures into the build processes to ensure integrity with the deployment of applications.
* Learn about the practical attacks that can happen against misconfigured CI/CD pipelines and build processes.

![That was the worst](/assets/images/CICD/26.PNG)

_As a note if you're completing this room - make sure to take notes at different intervals for credentials or syntax you may need_
![00](/assets/images/CICD/00.PNG)

#### Gathering information: getting the CICD IP - we need this throughout the room
![1](/assets/images/CICD/1.PNG)

#### Configure DNS
![2](/assets/images/CICD/2.PNG)

#### Makre sure you can access the login page at Gitlab - this tells you if you are connected properly
![3](/assets/images/CICD/3.PNG)

#### Login to Mother and register your credentials (note these down, we use these later)
![4](/assets/images/CICD/4.PNG)

#### Sign into or register for Gitlab and create a fork of the project
![5](/assets/images/CICD/5.PNG)

#### Follow the instructions to install, run, and register a runner
![6](/assets/images/CICD/6.PNG)

#### Make a change in the README file on the repo
![7](/assets/images/CICD/7.PNG)

#### Once the pipeline is complete - open the web application to verify it's working by going to pipelines and then pipeline schedules on the left hand side
![8](/assets/images/CICD/8.PNG)

#### Login with the credentials from the repo and grab the flag
![9](/assets/images/CICD/9.PNG)

#### Make sure Gitlab pip package is installed
##### To do this I used the below syntax (at the time of writing, note this may change based on the upgrades to gitlab)
```
pip3 install python-gitlab==3.15.0
```

![10](/assets/images/CICD/10.PNG)

####  Grab you personal access token from the user settings > access tokens page. Click the copy button. 
![11](/assets/images/CICD/11.PNG)

#### Add token to the python script we copied from the THM website
![12](/assets/images/CICD/12.PNG)

#### Grep for "THM" within the Modile Application folder (unzipped)
```
grep -r "THM"
```

![13](/assets/images/CICD/13.PNG)

#### Flag found
![14](/assets/images/CICD/14.PNG)

#### Create a shell script and set up a listener (this syntax for after script is created)
```
python3 -m http.server 8080
````
![15](/assets/images/CICD/15.PNG)

#### Replace info i nthe kenins file
![16](/assets/images/CICD/16.PNG)

#### Scroll to the top of the room, we can see a host unlocked
![17](/assets/images/CICD/17.PNG)

#### Create a forked proect from the merge test
![18](/assets/images/CICD/18.PNG)

#### After merger request created (we are listening)
![19](/assets/images/CICD/19.PNG)

#### Get the first flag by following mother orders (log into mother first, she will give you directions)
![20](/assets/images/CICD/20.PNG)

#### This screenshot is the first part of task 7
![21](/assets/images/CICD/21.PNG)

#### Once in the target machine - go back to mother and ask for directions to get the flag
![22](/assets/images/CICD/22.PNG)

#### Create public key for DEV and PROD (This is the start of heavily relying on the youtube videos for assistance / I got stuck here a lot trying to figure out how it works)
![23](/assets/images/CICD/23.PNG)

#### Try the API key - here is updating the .gitlab-ci.yml file
![24](/assets/images/CICD/24.PNG)

#### Once we deploy / push the pipeline to get the secret in the file. (This is also the last flag)
![25](/assets/images/CICD/25.PNG)

* * * 

<button onclick="history.back()">Go Back</button>
