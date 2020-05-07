---
title: "Implications of P != NP and cryptography"
date: 2020-05-06T17:28:50-06:00
draft: true
---

As a CTFer, I think I extablished the point well enough that I like doing web-based exploits. However, CTFs and cybersecurity as a whole encompass far more than just what I do - reverse engineering, binary exploits, cryptography, networking, etc etc... 

To many, cybersecurity and the practise of "hacking" typically will bring to mind people wearing Anonymous masks and breaking into accounts by cracking people's passwords. And considering how often people's credentials are leaked, cracked, or otherwise jeaporidzed, the imagery is pretty logical. 

Passwords in particular undergo a pretty rigorous process of hashing, salting, encryption, and more to ensure that someone won't be able to figure them out. And, I mean, we've all seen the crazy and ridiculous rules some sites enforce upon password-making. But we all know _why_ sites do this: the wierder and harder a password is to even type, the harder it will be to crack. 

But define "harder" in this context: when someone is able to brute-force your password, they're typically automated it - some script or engine will guess thousands (if not MILLIONS) of letter,number, and special character string combinations until the script will enumerate to a string that exactly matches your pass. By the concept of permutations, the process of this script correctly guessing your password can depend on how complex(variety of characters), on how long (length of your password), and on how popular(please don't use "password" as a password) it is. And, we've seen all the lists of the most commonly used passwords scraped from plenty of data leaks and hacks, so we should know this pretty comfortably at this point. 

When it comes to the process of cracking a password, to make it "hard" is to force the password-guessing script to go through an almost infinite amount of string permutations - the upper bound of it will likely be infinity. So in this context, to make your password "hard" to crack, is to make it so that it takes several lifetimes for a computer to blindly guess it. 

So great, there exists technologies and techniques and algorithms that fuzz and salt our passwords to the point where a computer blindly guessing it letter by letter will need a very long time to correctly get it. Cool! So what does this have to do with "P != NP"?

_everything_.

DISCLAIMER: I'm neither an expert on cryptography nor on algorithm analysis. This is an in-depth discussion for those who may not know what "P=NP" even means, or those who would like to get a bit of a headstart to learn more about either subject. 

The Basics
----

What even is P? 
Im really grateful to have had an outstanding algorithms class. The professor and TAs have been so immensely informational, I garnered alot of learning from this course! Among the topics I learned was the concept of "P": polynomial time. Any problem that has a verified solution which a computer can run in O(nk) is in P. 
So, think problems like sorting an array, determing if a graph has a cycle, or even multipying 2 numbers together. All of these problems have solutions that run in polynomial time. 
So, naturally, there exist problems absolutely definitely 100% NOT in P. But what about problems that, we don't really know if they're in P or not? Meaning, we can neither prove nor disprove the existence of some solution to a problem that can run in O(nk). Think, the travelling salesman problem, coloring a graph an arbitrary amount of colors, etc etc...

 