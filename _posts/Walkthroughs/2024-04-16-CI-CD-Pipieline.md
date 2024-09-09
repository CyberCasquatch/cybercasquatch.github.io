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

# photo1

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

# photo2

_As a note if you're completing this room - make sure to take notes at different intervals at credentials or syntax you may need_
# photo3

#### Gathering information: getting the CICD IP - we need this throughout the room
# photo4

#### Configure DNS
# photo5

#### Makre sure you can access the login page at Gitlab - this tells you if you are connected properly
# photo6

#### Login to Mother and register your credentials (note these down, we use these later)
# photo7

#### Sign into or register for Gitlab and create a fork of the project
# photo8

#### Follow the instructions to install, run, and register a runner
# photo9

#### Make a change in the README file on the repo
# photo10

#### Once the pipeline is complete - open the web application to verify it's working by going to pipelines and then pipeline schedules on the left hand side
# photo11

#### Login with the credentials from the repo and grab the flag
# photo12

#### Make sure Gitlab pip package is installed
##### To do this I used the below syntax (at the time of writing, note this may change based on the upgrades to gitlab)
```
pip3 install python-gitlab==3.15.0
```

# photo13

####  Grab you personal access token from the user settings > access tokens page. Click the copy button. 
# photo14

#### Add token to the python script we copied from the THM website
# photo14

#### Grep for "THM" within the Modile Application folder (unzipped)
```
grep -r "THM"
```

# photo15

#### Flag found
# photo16

#### Create a shell script and set up a listener (this syntax for after script is created)
```
python3 -m http.server 8080
````
# photo17

#### Replace info i nthe kenins file
# photo18

#### Scroll to the top of the room, we can see a host unlocked
# photo19

#### Create a forked proect from the merge test
# photo20

#### After merger request created (we are listening)
# photo21

#### Get the first flag by following mother orders (log into mother first, she will give you directions)
# photo22

#### This screenshot is the first part of task 7
# photo23

#### Once in the target machine - go back to mother and ask for directions to get the flag
# photo24

#### Create public key for DEV and PROD (This is the start of heavily relying on the youtube videos for assistance / I got stuck here a lot trying to figure out how it works)
# photo25

#### Try the API key - here is updating the .gitlab-ci.yml file
# photo26

#### Once we deploy / push the pipeline to get the secret in the file. (This is also the last flag)
# photo27
