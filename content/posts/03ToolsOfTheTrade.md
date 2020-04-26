---
title: "Commonly Used Software"
description: "My favourite tools to use for web-based exploitss"
date: 2020-04-24T01:18:38-06:00
draft: true
feature_image: https://tinyurl.com/syqrnv6
---

I really like web-based exploits so I focus primarily on the web challenges when my team and I participate in CTFs.

<!--more-->

 I typically take over the web challenges while my teammates delve into other topics - sometimes I look at the pwn problems but I'm definitely not as experienced/skilled as some of my teammates are :P Anyway, as someone who focuses on web stuff, I found myself constantly utilizing the same types of tools and software to help me with challenges. I thought I'd share them here! 


Web exploits are broad in topic and there definitely doesn't exist one tool for all sploits. However, as I got exposed to more challenges I found patterns in the problems. These patterns indicated the vulnerability that I should be taking advantage of, and therefore, what types of tools I needed to further investigate those vulnerabilities.

However, before I get into the list I would like to make a statement about the nature of this post. This is NOT a cheatsheet on how to hack into your friend's facebook. "Hacking" for malicious purposes is illegal and can cost you [jail time and hefty fines](https://criminal.findlaw.com/criminal-charges/hacking-laws-and-punishments.html). **I will not divulge information on how to compromise the privacy of individuals without their consent.** This is a CTF and ethical hacking blog only - what this post is, is a list of tools I found useful when scoping out web challenges for various CTFs! 

Let's Begin!
----

<h3> 1. Curl </h3>

https://curl.haxx.se/

A must! Curl is short for "Client URL". It's a simple and lightweight command-line tool to transfer data using various network protocols. I use it for basic GET and POST HTTP requests, but its so versatile it can be used for much more! In its simplest form, Curl is amazing to send data to and from a specified server. 

[In my CONfidence 2020 write-up for the "Cat Web" problem](https://jamvie.net/posts/confidence2020_01/), I use cURL heavily to examine the response data from the server with each post request I make. 

<h3> 2. Wireshark </h3>

https://www.wireshark.org/

I gained knowledge in wireshark back in my internet computing course days. I recieved a tutorial on the basics of how to use this software - it's a protocol analyzer designed to examine web traffic and capture packet data between two endpoints. This software can show and display packets on all levels of the OSI hierarchy structure - you can see your common HTTP packets, TCP, DNS, to name a common few...

What's important and vital about Wireshark is just how many packets it can analyze - its great for just checking out internet trafffic from your router, even! 

Plenty of forensic-based problems will give you a capture of internet traffic and hide a flag in there, so wireshark is useful even beyond the web challenges I use it for! 

<h3> 3. Burpsuite </h3>
![Burpsuite Feature]()

https://portswigger.net/burp



<h3> 4. Postman </h3>

https://www.postman.com/

If I need to send more complex requests that might make cURL more complex to use, Postman is my go to software. I don't just use Postman to send heavier requests to a server, it's also great for testing out RESTful APIs if you're creating one for a personal project. It has a clean UI and is extremely beginner-friendly, so Postman is my go-to for literally anything requiring RESTful API testing. 



