---
title: "2021: Wrapped"
date: 2021-12-20T22:37:46-08:00
draft: true
comments: false
images:
categories:
  - miscellaneous
tags: 
  - misc
  - wrapped
---

Wow, everyone's following along on Spotify's year-wrapped idea. ~~Data collection is so neat guys :)~~

This post is somewhat ironic, given how busy I got this year with a bunch of things and not being able to find the time to update my blog. My original plans for this when I started in 2020 were to give a writeup for at least 1 challenge per CTF, and write a few other interesting articles that I figured may be worth reading.

Anyway, a little over a year later, I'm now at a backlog of writeups and other articles. Several posts that I've been telling myself to finish this year are likely going to carry over onto my 2022 todo list. Life comes at you fast, I guess. 

## CTFs 
When I had the time, I would learn something new by checking out some high-profile CTF. DEF CON Quals was a highlight of this year, giving me a good opportunity to gain more confidence in myself as a security researcher. ASIS Quals was also a good highlight, giving me the opportunity to try out different XS-Leak attacks - a specific brand of websploits I found myself really liking.
Later into the year things got really busy with other areas of my life, so I wasn't able to participate in every CTF I wanted to. A shame, really, but next year is looking promising, and I'm excited to see what new ~~log4j~~ web-based challenges will appear in the future. 

Web-based security is a broad field, even within computer security, so it's hard to stay afloat above all the new things that could screw up your website. That being said, it's still a joy to learn about them all (from an incredibly weird and detached security researcher perspective. It's been horrifying to learn about them all as a Regular and Adjusted Internet User). I'm coming out of 2021 with far more depth to my knowledge. I've tried new things out, learned a few tricks, understood web-based topics with a closer eye. I'm still learning, and I have a ways to go, but it's nice being able to look back and see the progress I've made. 

### A minor analysis of the websploits I did
Following the broad scope of web, I wanted to see if there were any metrics I could talk about that were worthwhile. 
I got curious so I scraped all the challenges I solved and worked through to see if there were any common patterns (besides them being all web problems el oh el) among them. For each challenge I work through, I add in some quick notes about the solution and tags about the vulnerabilities that were required for it. I used these notes to congregate some data here. Expect some overlap as some challenges involved a spectrum of vulnerabilities and bugs to exploit together in a chain.

Out of the web-based challenges I tackled, a significant majority of them involved XSS exploits in some way, shape or form. 

Some notable favourites:
- [JustCTF's BabyCSP challenge](/posts/2021/01/justctf-2020-a-collection-of-web-problems/) 
- [ASIS Qual's Lovely Nonces challenge](/posts/2021/10/asis-quals-2021-lovely-nonce/)
- [RaRCTF's FBG challenge](/posts/2021/08/rarctf2021-some-simpler-web-probz/) (purely because I got first blood and had the pleasure of seeing the announcement "maple rawr uwu nyaa got first blood")
{{< image src="/images/2021Wrapped_firstblood.png" alt="webgang is sad" position="center" style="border-radius: 8px;" >}}

The 2nd biggest piece of the pie are challenges involving some sort of XS-Leak or XS-Search attack, which tracks following my thoughts on ASIS Quals. 

- ASIS Quals Lovely Nonces (again)
- pbCTF's Vault challenge
- UIUCTF's Yana challenge

The remaining topics that are worthy to note are insecure deserialization exploits, command injection exploits, and prototype-pollution exploits. A few others popped up here and there (an SSRF here, an SQLi there), making up a catch-all "other" category. 

- [A nice and simple challenge from Zh3r0 CTF](https://github.com/jamiepoli/CTF_Scripts/blob/main/Zh3r0/sparta.js)
- [UTCTF 2021's Tar Inspector challenge](/posts/2021/03/utctf-2021/#tar-inspector)
- Some challenges from CyberApocalypse
- Some nice proto pollution challenges from RedPwnCTF
- [DiceCTF's Build A Better Panel challenge](posts/2021/02/dicectf-2021/#build-a-better-panel)
- [DEF CON Quals' Threefactooorx challenge](posts/2021/04/def-con-quals-2021-getting-gud-threefactooorx)

## Security, in general
When 2021 began, the new hotness leaving 2020 was the [Solarwinds Orion](https://www.reuters.com/article/us-usa-cyber-treasury-exclsuive/suspected-russian-hackers-spied-on-u-s-treasury-emails-sources-idUKKBN28N0PG?edition-redirect=uk) hack that targeted enterprisal software used by thousands of other companies. Ransomware hidden in a software update, anyone?

Wait, sorry no I meant the [Microsoft Exchange](https://www.itpro.co.uk/security/zero-day-exploit/358760/microsoft-exchange-zero-day-hack) hack, which involved a vulnerable version of exchange based on the proxylogon features in the infrastructure which could allow for authentication bypass and RCE.

Oh actually I meant the [Twitch Data Dump](https://blog.twitch.tv/en/2021/10/15/updates-on-the-twitch-security-incident/) where an anonymous user leaked 125 GB worth of source code, interior discussions, and private commits from Twitch's data to the internet. Rough year for streaming, huh?

Wait no I meant the [log4j](https://www.helpnetsecurity.com/2021/12/20/log4j-attack-vectors/) hack, which was a huge insecure deserialization bug in the Java implementation of LDAP with the JNDI API but what if in Minecraft? No? Okay how about **anywhere and everywhere that log4j is being used**? 

I wouldn't argue for 2021 being a safe year for computer security, to say the least. 

## Me, in general 
2021 has been a hell of a year, COVID not-withstanding. Aside from suddenly renewing my knowledge in the Greek alphabet, 2021 was a year of gifts and endless responsibilities. 

Developing my professional and academic life has been nothing short of a wild ride. It's been a fantastic journey getting hands on experience with the topics of computer security and vulnerability research, while also diversifying; developing different areas of my technical skillset through discovering a passion for combinatorial optimization (graph theory _is_ quite fun, actually. You were right, Donald Knuth).

Funny to think how 1 year could bring about so much change and growth. Scale this up to a decade and the difference is night and day. Would a younger me circa 2011 have believed the circumstances of my life now? Learning, growing and contributing the computer security researcher community in a way that was meaningful? Hacking for fun? _Liking math?_ Probably not, no. But I guess that's not really important. I'm excited for the new year, and to see what 2022 has in store for me and computer security. 