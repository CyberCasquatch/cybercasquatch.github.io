---
layout: post
title:  "NOTES: Deobfuscation: Javascript Deobfuscation"
date: 2025-03-10
author: C. Casquatch
comments: false
tag: ['javascript', ‘deobfuscation’, 'htb']
---


Code Analysis
=============

We've successfully deobfuscated the code, it's time to start breaking it down and seeing what it's doing.

    `'use strict';       
    function generateSerial() {         
    ...SNIP...         
    var xhr = new XMLHttpRequest;         
    var url = "/serial.php";         
    xhr.open("POST", url, true);         
    xhr.send(null);       };`
    
  

From this, we can tell that the `secret.js` file only contains one function: `generateSerial`.

HTTP Requests
-------------

Let’s break down the `generateSerial` function, starting from the first line.

### Code Variables

The function begins by creating a variable `xhr`, which is an instance of the `XMLHttpRequest` object. If we’re not familiar with `XMLHttpRequest`, we could Google it to understand its purpose. After a quick search, we learn that it’s a JavaScript object that allows us to send HTTP requests to a server.

The second variable is `url`, which holds the string `/serial.php`. This URL is relative, so it's likely pointing to a resource on the same domain, given that no full domain is specified.

### Code Functions

Next, we see `xhr.open()` being called with "POST" and the URL as parameters. Again, a quick search helps us understand that `xhr.open` prepares the request by defining the method (GET or POST) and the target URL. Then, `xhr.send(null)` sends the request.

So, in essence, the `generateSerial` function is sending a POST request to the `/serial.php` endpoint without any data in the request body, nor is it expecting anything in return.

The way this function is written suggests it’s part of a bigger feature—perhaps one that generates serials when triggered by an event, like clicking a button. However, since we didn’t find any corresponding HTML elements (such as a "Generate Serial" button), it seems like the developers may have written this function for future use, but haven’t implemented it yet.



  Secret Serial Generator \* { margin: 0; padding: 0; border: 0; } html { font-family: Arial, sans-serif; } h1 { font-size: 2em; color: #333; } p { font-size: 1.2em; color: #666; }

Secret Serial Generator
=======================

HTTP Requests
-------------

In the previous section, we uncovered that the primary function within `secret.js` is making an empty POST request to `/serial.php`. Now, we’re going to replicate this action manually using cURL to send a POST request to the same location. If you’re unfamiliar with cURL or web requests in general, you may want to explore the **[Web Requests module](#)** for more details.

Using cURL
----------

cURL is a powerful and versatile tool that can be used on Linux, macOS, and Windows PowerShell. It allows you to interact with websites directly from the command line, making web requests a breeze. For example, with a simple cURL command, you can request the content of a webpage and receive the output as plain text:

    curl http://SERVER_IP:PORT/

If you run this command, the server will respond with the page content, which could look something like the following:

    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Secret Serial Generator</title>
        <style>
            * {
                margin: 0;
                padding: 0;
                border: 0;
            }
    
            html {
                font-family: Arial, sans-serif;
            }
    
            h1 {
                font-size: 2em;
                color: #333;
            }
    
            p {
                font-size: 1.2em;
                color: #666;
            }
        </style>
    </head>
    
    <body>
        <div>
            <h1>Secret Serial Generator</h1>
            <p>This page generates secret serials!</p>
        </div>
    </body>
    
    </html>
    

This is exactly what we encountered earlier when we inspected the source code of the page.

Sending a POST Request
----------------------

To replicate the POST request, we need to modify our cURL command slightly. By using the `-X POST` flag, we tell cURL to send a POST request rather than a GET request:

    curl -s http://SERVER_IP:PORT/ -X POST

**Tip:** The `-s` flag is used to suppress extra output and prevent unnecessary information from cluttering the response.

While this sends the POST request, it doesn’t include any data. Typically, POST requests carry data that is sent to the server. To add data, we can use the `-d` flag, followed by the parameters we wish to send. For instance:

    curl -s http://SERVER_IP:PORT/ -X POST -d "param1=sample"



<button onclick="history.back()">Go Back</button>
