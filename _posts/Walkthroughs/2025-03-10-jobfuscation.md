---
layout: post
title:  "NOTES: Obfuscation: Javascript Obfuscation"
date: 2025-03-10
author: C. Casquatch
comments: false
tag: ['javascript', 'obfuscation', 'htb']
---

* * *

Before we jump into learning about deobfuscation, it’s important to get a good understanding of what code obfuscation actually is. Without knowing how code gets obfuscated in the first place, it would be pretty tough to try and reverse or deobfuscate it — especially if a custom obfuscator was used.

* * *

What is Obfuscation?
--------------------

Obfuscation is basically a way to make code harder for humans to read, while still allowing it to work the same when executed. Sometimes, the code may even run a bit slower after obfuscation, but the main goal is to make it difficult to understand. Usually, this is done automatically using a tool called an _obfuscator_, which takes normal code as input and rewrites it to look like a confusing mess — depending on how it’s designed.

For example, a lot of obfuscators will take all the words and symbols used in the code and create a dictionary from them. Then, when the code runs, it pieces everything back together by referencing that dictionary. Below is a basic example of JavaScript code that has been obfuscated.

Languages like JavaScript, Python, and PHP are interpreted, meaning they run the code directly without compiling it into an executable. While Python and PHP typically run on the server-side (so users don’t see the code), JavaScript runs right in the web browser — so the code is sent in cleartext for anyone to see. That’s one big reason why JavaScript is commonly obfuscated, to hide what it’s doing.

* * *

Use Cases
---------

There are a bunch of reasons why developers might want to obfuscate their code. One of the main reasons is to stop others from copying or reusing their code without permission. By making the code harder to read, it’s also harder to reverse engineer or figure out what the code is doing.

Another reason for obfuscation is to add a layer of protection around sensitive operations like authentication or encryption. But, it’s important to mention that doing authentication or encryption on the client-side is generally not recommended — since that code can be exposed and attacked more easily.

That being said, one of the most common (and probably most worrying) uses of obfuscation is for malicious purposes. Attackers often obfuscate their scripts to avoid detection by Intrusion Detection Systems (IDS) or Intrusion Prevention Systems (IPS). If the script is hard to read, automated tools might miss it.

In the next part of this walkthrough, I’ll be looking at how to obfuscate a simple JavaScript script and compare how it runs before and after the obfuscation process. It’ll be a good way to see the impact and what changes when code gets obfuscated.




Basic Obfuscation
=================

There are plenty of tools out there for different programming languages that can handle this automatically.

* * *

Running JavaScript Code
-----------------------

To start, let’s take a simple line of JavaScript code and have a go at obfuscating it. Here’s the line we’ll be working with:

    console.log('HTB JavaScript Deobfuscation Module');

Before we attempt to obfuscate anything, let’s first run this code in its original, cleartext form to see what it does. Head over to [JSConsole](https://jsconsole.com/), paste this line in, and press enter. You should see the output:

> HTB JavaScript Deobfuscation Module

All this line does is print that message to the console, using the `console.log()` function — nothing fancy, but it’s a good starting point.

* * *

Minifying JavaScript Code
-------------------------

One of the most common ways to make JavaScript code less readable — without changing how it works — is by minifying it. Minification basically compresses the code into a single line (which can get pretty long), and while it doesn’t technically "obfuscate" it fully, it definitely makes it harder to read at a glance.

Minification is especially useful for longer pieces of code. In our case, since we only have one line, the result won’t look too different. Still, let’s give it a shot using [javascript-minifier](https://javascript-minifier.com/). Just paste in the code, click "Minify", and you’ll see the compressed output on the right side.

If you copy and paste this minified version back into [JSConsole](https://jsconsole.com/), you’ll see that it runs exactly the same. Usually, when developers minify JavaScript, they save it as a `.min.js` file.

**Note:** Minification isn’t limited to JavaScript. Other languages can also be minified — something to keep in mind when looking for ways to compress code.

* * *

Packing JavaScript Code
-----------------------

Now let’s move a step further and actually obfuscate this line of code to make it much harder to read. For this, we’ll use a tool like [BeautifyTools JavaScript Obfuscator](https://beautifytools.com/javascript-obfuscator.php).

Here’s an example of what the output might look like after obfuscation:

    eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('5.4(\'3 2 1 0\');',6,6,'Module|Deobfuscation|JavaScript|HTB|log|console'.split('|'),0,{}))

As you can see, this version is much harder to read. But if you copy and paste this obfuscated code back into [JSConsole](https://jsconsole.com/), you’ll notice that it still performs the same task — printing our message.

**Note:** This style of obfuscation is known as "packing", and you can usually spot a packed script by the six-argument function format — `function(p,a,c,k,e,d)` — used at the start.

So, what does a packer do? Essentially, it takes all the words and symbols from the original code, puts them into a dictionary or list, and then refers to them dynamically when running the script. This is what makes unpacking or reading the code a bit of a headache. The argument names like `(p,a,c,k,e,d)` might vary between packers, but the general idea stays the same: these variables help the browser reconstruct the original code when it's executed.

While packing makes the code tougher to read, you’ll probably notice that some of the main strings, like text messages, still appear in plain text. That’s why attackers or anyone looking to hide code may want to combine this with even more advanced obfuscation techniques for extra stealth.

* * *




Advanced Obfuscation
====================

In this section, I’m going to walk through some tools that can help us take our obfuscation to the next level — fully hiding what the code does and leaving no obvious traces of its original purpose.

* * *

Using [obfuscator.io](https://obfuscator.io)
--------------------------------------------

First, let's head over to [https://obfuscator.io](https://obfuscator.io). Before we click the 'Obfuscate' button, we’ll adjust one of the settings. Under "String Array Encoding", make sure you set this to **Base64** — this will help encode all string data more securely.

Once you've done that, paste in your JavaScript code and click 'Obfuscate'. You should now see something similar to the following:

    
    // Example obfuscated code
    var _0x1ec6=['Bg9N','sfrciePHDMfty3jPChqGrgvVyMz1C2nHDgLVBIbnB2r1Bgu='];(function(_0x13249d,_0x1ec6e5){var _0x14f83b=function(_0x3f720f){while(--_0x3f720f){_0x13249d['push'](_0x13249d['shift']());}};_0x14f83b(++_0x1ec6e5);}(_0x1ec6,0xb4));
    var _0x14f8=function(_0x13249d,_0x1ec6e5){...};console[_0x14f8('0x0')](_0x14f8('0x1'));
    

As you can see, this version is much more obfuscated. There are no obvious strings left to give clues about what the code does. If you want to check that it still works, head over to [https://jsconsole.com](https://jsconsole.com), paste it in, and run it. It should still perform its original function, even though it's now unreadable.

Feel free to play around with the other options on [obfuscator.io](https://obfuscator.io) to see if you can make the code even more confusing, but just make sure to check in [JSConsole](https://jsconsole.com) that it still runs properly!

* * *

Exploring More Obfuscation Techniques
-------------------------------------

By now, you should have a good sense of how code obfuscation works and how it can make JavaScript almost impossible to read. But there are plenty of other tools and ways to obfuscate code, each producing different results.

Here’s an example of some heavily obfuscated JavaScript using other techniques:

    
    // Example of extreme obfuscation
    [][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+...SNIP...
    

(Note: I’ve snipped the full code as it's very long, but it would still run correctly.)

You can take this code, run it in JSConsole, and you’ll see that it still does what it was originally meant to — even though we can’t easily understand how.

Interestingly, when we obfuscate code this way and then run it, you might notice it takes longer to execute. This is a great example of how extreme obfuscation can affect performance — something to keep in mind if you’re obfuscating large pieces of code.

* * *

Other JavaScript Obfuscation Tools
----------------------------------

Beyond [obfuscator.io](https://obfuscator.io), there are some other well-known tools you can experiment with:

*   [JSFuck](https://www.jsfuck.com/) (produces JavaScript made entirely out of special characters)
*   JJ Encode (search online for online tools)
*   AA Encode (search online for online tools)

Just a heads-up though — tools like JJ Encode and AA Encode can make code extremely slow to run or even impossible to compile on some systems. They are typically only used for specific reasons, like trying to bypass web filters or restrictions, so they aren’t always practical for general use.

* * *



Deobfuscation
=============

* * *

Now that we’ve covered how code obfuscation works, it’s time to start looking at how we can reverse that process — also known as **deobfuscation**. Just like there are tools to obfuscate code automatically, there are also tools that help us _beautify_ and deobfuscate code, making it easier to read and understand.

* * *

Beautify
--------

The JavaScript code we’re working with is currently written all on one line — this is called **minified** JavaScript. To make sense of it, our first step is to properly format (or "beautify") the code.

One of the easiest ways to do this is by using the Developer Tools built into your web browser. For example, if you’re using Firefox, you can open the debugger by pressing CTRL + SHIFT + Z. From there, you can open `secret.js` — and although it may still look like a mess, clicking the **{ }** button at the bottom will _Pretty Print_ the script, turning it into a much more readable format.

Besides browser tools, there are also plenty of online beautifiers and code editor plugins you can use, such as [Prettier](https://prettier.io/) or [Beautifier](https://beautifier.io/).

Let’s take this example of the `secret.js` script we’ve been analysing:

    
    eval(function (p, a, c, k, e, d) { 
      e = function (c) { return c.toString(36) }; 
      if (!''.replace(/^/, String)) { 
        while (c--) { 
          d[c.toString(a)] = k[c] || c.toString(a) 
        } 
        k = [function (e) { return d[e] }]; 
        e = function () { return '\\w+' }; 
        c = 1 
      }; 
      while (c--) { 
        if (k[c]) { 
          p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]) 
        } 
      } 
      return p 
    }('g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}', 17, 17, 'var|xhr|url|null|generateSerial|flag|HTB|flag|new|serial|XMLHttpRequest|send|php|open|POST|true|function'.split('|'), 0, {}));
    

After using one of these beautifiers, you’ll notice that although the code is now nicely formatted, it still doesn’t make much sense — that’s because this script has been both minified **and** obfuscated. So, just pretty printing or formatting it won’t be enough. To really get to the bottom of it, we’ll need to properly **deobfuscate** it.

* * *

Deobfuscate
-----------

Luckily, there are many useful online tools designed to deobfuscate JavaScript and make it human-readable. One great tool for this is [UnPacker](https://lelinhtinh.github.io/de4js/).

If we copy our obfuscated script into UnPacker and hit the **UnPack** button, we can see that it does a much better job of unravelling the code and presenting something understandable.

**Tip:** Make sure you don't have any empty lines before the script when you paste it, as this might affect how well the tool works.

Here’s the result after deobfuscating:

    
    function generateSerial() {
      // ...SNIP...
      var xhr = new XMLHttpRequest;
      var url = "/serial.php";
      xhr.open("POST", url, true);
      xhr.send(null);
    };
    

As we can see, this is much easier to interpret now. The technique used here is known as **packing**. Another way to handle such obfuscated scripts is to locate the return value and instead of executing it, use `console.log()` to print it out for analysis.

* * *

Reverse Engineering
-------------------

While online tools like UnPacker do a fantastic job for many cases, sometimes the code is so heavily obfuscated — or uses custom-made obfuscation techniques — that automated tools won’t be able to fully clean it up.

In these tougher cases, you’ll need to dive into manual reverse engineering. This involves working through the code line by line to figure out how it’s been obfuscated, and what its true functionality is.



<button onclick="history.back()">Go Back</button>
