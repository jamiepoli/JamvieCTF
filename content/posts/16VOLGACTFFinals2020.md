---
title: "VolgaCTF Finals 2020"
date: 2020-09-19T20:57:15-06:00
draft: false
comments: false
tags: 
- misc
images:
---

A reflection on VolgaCTF Finals, which I participated in with my team. We were the only North American team participating in the event, and for a good portion of the CTF, we held 1st place! Unfortunately, that didn't last for the end of the competition. 
<!--more-->

This year of the finals round gave us an online offering of the attack-defense CTF competition, marking my first experience with attack-defense style CTFs. Overall the challenges and showmanship were alright throughout the competition, but a big barrier between my team and the others participating was the timezone. VolgaCTF was occuring in the midday in Russia, which was midnight in my timezone. 

My team and I had to pull an all-nighter to partcipate in the finals round, and the lack of sleep was definitely to our disadvantage. 

## The Setup
Since the round was online due to COVID-19, we did not participate in person. Our setup was all virtual: we used a VPN to connect to the VM instance given to us by the competition organizers. The setup was as so:

- We are given a unique vulnbox instance that are accessible only via the VPN connection, which we connected to on the day of. 

- The services (challenges) are all in the vulnbox, isolated via docker containers. There were 4 services in total, and we were responsible for their upkeep. This proved challenging - essentially adopting the role of sysadmin as we attempted to patch these services _and_ keep them running. Every team's instance of a service were being flooded by requests from other teams to get flags, so we unintentionally adopted DevOps roles alongside hacking. If a service was down for us on our server, we couldn't obtain flags from other servers while it was down. 

- Flags were to be configured as ``VolgaCTF{stuff here}`` kept in a unique, specific encoding. We had a script that automated flag decoding and submission. 


## The Challenges
The challenges were straightforward and almost all encompassed web-based exploits, which was a welcome developement for me. I focused on the last challenge, PDFer, as it was the longest unsolved service and I was obsessed with getting first blood on it :P. 

## Summary
This is my first attack-defense CTF. Having it fully online? An interesting experience. My mentors and other team members who had participated in more CTFs remarked that in-person attack-defenses are a completely different experience entirely, and I really want to see what that would be like (which I guess isn't anytime soon). The concept itself was cool, although the duty of maintaining the servers as a sysadmin was not necessarily something I was anticipating. Unfortunately, there was a limit to the amount of people who could participate in the finals, and if the limit was increased, more stratified and defined roles could have existed to allow members to focus on one specific thing in the CTF. Perhaps my next attack-defense will have a higher team limit. 

But overall, the experience was a unique one, and I am happy to have participated in the attack-defense CTF given it was my first. Hopefully, there will be many more to come in my future. 

Jam