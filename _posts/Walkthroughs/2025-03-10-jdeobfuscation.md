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




Decoding
========

In the previous section, we discovered a strange block of text that seemed to be encoded:

    curl http://SERVER_IP:PORT/serial.php -X POST -d "param1=sample"

    ZG8gdGhlIGV4ZXJjaXNlLCBkb24ndCBjb3B5IGFuZCBwYXN0ZSA7KQo=

This is another common example of obfuscation, as discussed in the [Advanced Obfuscation](#advanced-obfuscation) section. Several techniques can further obfuscate code, making it harder for humans to read and for systems to detect. As such, you'll often come across obfuscated code containing encoded text blocks that are decoded at runtime. In this section, we'll dive into three of the most common text encoding methods:

*   Base64
*   Hex
*   ROT13

Base64
------

Base64 encoding is commonly used to reduce the reliance on special characters. Any data encoded in base64 will be represented using alphanumeric characters and the symbols `+` and `/`. Regardless of the input (even binary data), the output will always follow this pattern.

### Spotting Base64

Base64 encoded strings are easy to spot because they only consist of alphanumeric characters. The most distinguishing feature is its padding, denoted by the `=` character. Since the output length of base64 must be a multiple of four, an extra `=` is added if necessary.

### Base64 Encode

To encode any text into base64 in Linux, you can use the following command:

    echo https://www.hackthebox.eu/ | base64

Output:

    aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K

### Base64 Decode

If you'd like to decode a base64 encoded string, simply use the `-d` option as shown below:

    echo aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K | base64 -d

Output:

    https://www.hackthebox.eu/

Hex Encoding
------------

Hex encoding is another common method, where each character is converted into its hexadecimal equivalent based on the ASCII table. For example, the character `a` becomes `61` in hex, `b` becomes `62`, and so on. You can view the full ASCII table on your system by using the `man ascii` command.

### Spotting Hex

Hex encoded strings are easy to spot because they only contain hex characters, which range from `0-9` and `a-f`. This makes it quite straightforward to distinguish hex-encoded strings from other formats.

### Hex Encode

To encode a string into hex in Linux, you can use the following command:

    echo https://www.hackthebox.eu/ | xxd -p

Output:

    68747470733a2f2f7777772e6861636b746865626f782e65752f0a

### Hex Decode

To decode a hex string, use the `xxd -p -r` command as shown below:

    echo 68747470733a2f2f7777772e6861636b746865626f782e65752f0a | xxd -p -r

Output:

    https://www.hackthebox.eu/

Caesar/ROT13
------------

ROT13 is a simple cipher that shifts each letter by 13 places in the alphabet. It is a specific variation of the Caesar cipher, which shifts letters by a fixed number of positions. Although it was once widely used, it is now considered a basic form of obfuscation.

### Spotting Caesar/ROT13

While ROT13 may make the text look random, it is still easy to spot. For example, a URL like `http://www` would become `uggc://jjj`, which still retains recognizable patterns that may alert you to the use of ROT13.

### ROT13 Encode

There isn't a built-in command for ROT13 in Linux, but you can use the `tr` command to shift characters as follows:

    echo https://www.hackthebox.eu/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'

Output:

    uggcf://jjj.unpxgurobk.rh/

### Online Tools

For both encoding and decoding ROT13, you can also use online tools like [rot13](https://www.rot13.com/).

Other Types of Encoding
-----------------------

While we've covered the most common encoding methods, there are hundreds of others available online. Sometimes you may come across unfamiliar encoding types, which could require a bit more experience to identify and decode effectively.

If you're ever faced with unfamiliar encoding, try to determine the type first, and then look for online tools to decode it. A helpful resource is [Cipher Identifier](https://www.guballa.de/cipher-identifier), which can automatically identify different encoding methods. You can test the encoded strings above using Cipher Identifier to see how accurately it identifies the encoding.

In addition to encoding, some obfuscation tools use encryption, which encodes strings using a secret key. This can make it much harder to reverse-engineer the obfuscated code, especially if the decryption key isn't present within the script itself.



<button onclick="history.back()">Go Back</button>
