---
title: "Commonly Used Software"
description: "My favourite tools to use for web-based exploits"
author: "Jamvie"
date: 2020-04-26T01:30:30-06:00
draft: false
feature_image: https://tinyurl.com/syqrnv6
---

I really like web-based exploits, so I focus primarily on the web challenges when my team and I participate in CTFs.

<!--more-->

 
I typically take over the web challenges while my teammates delve into other topics - sometimes I look at the pwn problems, and hopefully I'll include some pwn writeups here! Eventually :P Anyway, as someone who focuses on web stuff, I found myself constantly utilizing the same types of tools and software to help me with challenges. I thought I'd share them here! 

Web exploits are broad in topic and there definitely doesn't exist one tool for all sploits. However, as I got exposed to more challenges I found patterns in the problems. These patterns indicated the vulnerability that I should be taking advantage of, and therefore, what types of tools I needed to further investigate those vulnerabilities.

However, before I get into the list I would like to make a statement about the nature of this post. This is NOT a cheatsheet on how to hack into your friend's facebook. "Hacking" for malicious purposes is illegal and can cost you [jail time and hefty fines](https://criminal.findlaw.com/criminal-charges/hacking-laws-and-punishments.html). 

**I will not divulge information on how to compromise the privacy of individuals without their consent.** 
This is a CTF and ethical hacking blog only - what this post is, is a list of tools I found useful when scoping out web challenges for various CTFs! 

Let's Begin!
----

_These are not ordered by priority or "most utilized"._

---

<h3> 1. Curl </h3>

![cURL_Logo](https://raw.githubusercontent.com/jamiepoli/JamvieCTF/master/content/images/curl-logo.svg)\

<p align="center">
<a href=https://curl.haxx.se>cURL</a>
</p>

A must! cURL is short for "Client URL". It's a simple and lightweight command-line tool to transfer data using various network protocols. I use it for basic GET and POST HTTP requests, but its so versatile it can be used for much more! In its simplest form, cURL is amazing to send data to and from a specified server. 

[In my CONfidence 2020 write-up for the "Cat Web" problem](https://jamvie.net/posts/02confidence2020_01/), I use cURL heavily to examine the response data from the server with each post request I make. 

<h3> 2. Wireshark </h3>

<p align="center">
<img width="460" height="300" src="https://raw.githubusercontent.com/jamiepoli/JamvieCTF/master/content/images/WireSharkLogo.png">
</p>

<p align="center">
<a href= https://www.wireshark.org>WireShark Home Page</a>
</p>

I gained knowledge in Wireshark back in my internet computing course days. I recieved a tutorial on the basics of how to use this software - it's a protocol analyzer designed to examine web traffic and capture packet data between two endpoints. This software can show and display packets on all levels of the OSI hierarchy structure - you can see your common HTTP packets, TCP, DNS, to name a common few...

Plenty of forensic-based problems will give you a capture of internet traffic and hide a flag in there, so wireshark is useful even beyond the web challenges I use it for! 

<h3> 3. Burp Suite </h3>

<p align="center">
<img width="460" height="300" src="https://raw.githubusercontent.com/jamiepoli/JamvieCTF/master/content/images/Burpsuite_card.png">
</p>

<p align="center">
<a href=https://portswigger.net/burp>Burp Suite</a>
</p>

From [PortSwigger, creators of BurpSuite](https://portswigger.net/support/how-to-use-burp-suite):

>Burp Suite is an integrated platform for performing security testing of web applications. It is designed to be used by hands-on testers to support the testing process.

Burp Suite is perfect for testing out all sorts of exploits. It has much more use in testing non-production projects by enterprisal companies, and its a powerful tool for scoping out any possible vulnerabilities of an application. The community version comes with all the basic and necessary tools a CTFer or pentester would need to gauge the security of their application! 

Back in my [UTCTF "Epic Admin Pwn" writeup](https://jamvie.net/posts/01utctf01/), I mentioned using Burp Suite to scope out the SQLi attack vector and using SQLmap to further exploit it - which is a much faster way than creating your own script to utilize the attack vector that was found in it! 


<h3> 4. Postman </h3>

<p align="center">
<img width="460" height="300" src="https://raw.githubusercontent.com/jamiepoli/JamvieCTF/master/content/images/Postman.png">
</p>

<p align="center">
<a href=https://www.postman.com>Postman</a>
</p>

If I need to send more complex requests that might make cURL more complex to use, Postman is my go to software. I don't just use Postman to send heavier requests to a server, it's also great for testing out RESTful APIs if you're creating one for a personal project. It has a clean UI and is extremely beginner-friendly, so Postman is my go-to for literally anything requiring RESTful API testing. 

<h3> 5. SQLMap </h3>

<p align="center">
<img width="460" height="300" src="https://upload.wikimedia.org/wikipedia/commons/4/4f/Sqlmap_logo.png">
</p>

<p align="center">
<a href=http://sqlmap.org>SQLmap</a>
<p>

SQLmap is a suite of great utilities specifically for SQL-injection based attacks. It automates the detection and exploitation of any SQL-based flaws in an application. It supports most if not all major databases (MySQL, SQLite, etc) and can do everything from simple detection to database table dumping.

<h3> 6. Webhook.site </h3>

<p align="center">
<img width="250" height="300" src="https://d2.alternativeto.net/dist/icons/webhook-site_144103.jpg?width=200&height=200&mode=crop&upscale=false">
</p>



<p align="center">
<a href=https://docs.webhook.site>Webhook.site Docs</a>
<p>

I frequently alternate between generating a quick python-made server for XSS attacks, or using Webhook.site. This is a great tool if you don't want to make your own server or don't have your own website to host your payload javascript files for certain XSS attacks! 

---

While this is not an exhaustive list of all the tools I use, these are definitely among the most commonly used softwares that I go to when I need to test and utilize any vulnerabilities in a given CTF challenge. I hope these prove useful to you! 

Jam


References
----

[Feature Image](https://knowyourmeme.com/memes/hackerman)