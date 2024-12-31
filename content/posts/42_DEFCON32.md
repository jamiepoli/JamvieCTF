---
title: "DEF CON 32 Retrospective, end of the year recap"
date: 2024-12-29T10:29:46-04:00
draft: false
toc: true
images:
tags:
  - untagged
categories:
  - retrospectives 
---

In 2009, a group students at the Carnegie Mellon University formed a computer security club known as the [Plaid Parliament of Pwning](https://pwning.net/about/), abbreviated to PPP, to participate in Capture the Flag contests. The team saw numerous successes throughout their tenure, and many of the CMU folks with their roots in PPP went on to succeed professionally in cybersecurity-related careers.

2 such members went on to co-create [Theori.io](https://theori.io/), a leading platform of security solutions covering everything from security education to audits and consulting. Their presence exists in both South Korea and USA, and they have their own CTF team, [The Duck](https://github.com/theori-io/ctf/tree/master), that dominates leaderboards whenever they play. 

Up north, professor Robert Xiao, after completing his PhD at CMU, went on to become an assistant professor at the University of British Columbia in Vancouver, Canada. A seasoned CTF player, he brought more visibility into the world of CTFs to UBC, founding and creating the team [Maple Bacon](https://maplebacon.org/about/). 

Without alot of the support I got at Maple Bacon and MMM, I don't think I would be where I'm at today. That's a fact I think about often and keep close to me. It has now been almost 2 years since I graduated UBC, and about 4ish years since I got into CTFs as a whole. If you know me, than you would know I was once an artist in a past life. I think it's natural for me to compare my artistic journey to my hacker one and see the differences and similarities between the two, [which I have talked about before](https://www.youtube.com/watch?v=sMiFeCDqX50). But one difference was that as an artist I was alone. 

It kinda helped, I think, the isolation. With no one else to bother you, you only focus on what is in front of you: the canvas. I also know that you can only go so far on your own, at least in my case. I approached CTFs the same way I did with art, and it definitely led to some early successes - alot of the skills are transferrable (not, like, the painting skills. Obviously. I mean the more abstract "thinking out of the box" skills. Fuck it, you get what I mean). I think what got me farther was the fact that I _wasn't_ alone. When you have a group of hard working people around you, you're naturally ingratiated to be just as hard-working yourself. Maybe that mentality helped tremendously with our 3rd consecutive [DEF CON](https://nautilus.institute/) win this year. 

{{< image src="/images/MMM_hattrick.JPG"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

I'm really late with this, I'm aware. I've sort of combined this with a psuedo-retrospective end of year recap just so I don't have to release multiple different blog posts at once all covering similar principles. I'll try and format this blog post based on a selection of questions I'm frequently asked.

## How do you pronounce Vie?

Vie

## You're a web main - how do you feel about the lack of web representation in DEF CON?

I can do more than web stuff

## DEF CON was in August. You're a little late!

Hey thanks for noticing. 2024 kind of kicked my ass. I don't feel like trauma dumping on main here so I'll leave the details out and say that 2024 was a year I survived, instead of thrived. But that's alright, imo. Sometimes you forget who the fuck you are and you need some time and self-care to remember who the fuck you are. Sorry I'm late. 

## Tell me more about DEF CON.

### Thursday

DEF CON being at the LVCC was annoying in terms of needing to carpool to get to the convention, as opposed to walking there from our hotels, but this is ultimately a first-world complaint and something I forgot about during the entire CTF. 

I love Vegas. It's a pretty city. I spent the first day hanging out with MMM, getting shit set up, and getting my badge from the convention center. I went to Nobu for a fancy dinner and damn, I gotta say, the miso black cod is worth the hype.

{{< image src="/images/misoblackcod.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

### Friday

Before getting super involved in DEF CON, I was also present in Vegas for a special talk at [Google's init.g event](https://www.unlv.edu/announcement/howard-r-hughes-college-engineering/google-sponsors-initgvegas-student-event-unlv). I did a talk covering some red teaming concepts with Zeta, which went pretty well! [Here's a video where LiveOverflow shared some Android hacking tips, as part of init.g](https://www.youtube.com/watch?v=fPt6fJDjKKM).

{{< image src="/images/DEFCON_BADGE.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

I really liked the badges that they did for this year. You can turn the cat shape upside down to have an ergonomic gaming controller and play the game in the badge challenge. 

{{< image src="/images/DEFCON32_black.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

The black badge variant was also very cool: laced with gold and swarovski crystals, and I think radioactive isotope? Someone fact check me on that I can't remember.

In a massively good move, patches submitted to the CTF infrastructure would be rejected before being uploaded to the public docker registry if the patch failed the SLA CI/CD tests. This was huge, removing the SLA component in the general scoring formula (making it rely solely on attack, defense, liveCTF and KoTH) and having it be tests that our patch would have to pass removed alot of potential headache of downed services or other SLA-failing issues we dealt with in the past. Naturally we wouldn't know what the tests were actually doing cause then patch-making would be easy, but this simple switch saved us some headaches we were accustomed to getting in previous years. 

In an attack defense CTF, whoever first bloods gets a massive advantage, and that was not us. Several teams managed to pop off exploits before us and that meant a whirlwind Friday as our first day. There were 8 A/D challenges: 

* bizbee
* lazelle
* codewords
* backflip
* cloud-cache --> I looked at this one the most
* helium
* bisbeebee
* sokoban, released on day 1 and retired day 3.

We also had a KoTH challenge with LLMs: llmship, which had since been a thing from 2022, and I assume is just going to be a mainstay from here on out. This challenge seemed to be a callback to [ropship AI](https://ctfradi.ooo/2020/10/06/005-ropship-ai-with-antonio-and-ppp.html), but with actual AI (LLMs). Unfortunately, LLM challenges involve randomness that sort of prioritizes prompts on a metric completely unknown and random to us, and it felt like we were very unlucky with this challenge. 

Friday left us a bit wrought out, but we maintained our determination into the next day.

### Saturday

I was really busy and wasn't able to hang out with some coworkers, but this day I went down to the floor and got some photos taken of the new venue. LVCC is awfully nice and I do appreciate how spacious the place was.

Here's the situation: for a while, we (MMM) were at 5th place for a good chunk of the day. In spite of our numbers alot of us were running around patching, identifying vulns, and otherwise getting confused why patches were getting rejected. DEF CON always brings the best so we were expecting a fight, and dare I say, we were getting burned out.

I don't have much else to say for this day, just that we were still working hard but getting frustrated at the little fires we had to put out. We ended Saturday in 2nd place, but we all knew we wanted only one position.

### Sunday

In a phenomena I can only describe as a Collective Dislike Of The Circumstances We Were In, each and every single one of us at MMM all unanimously agreed to stand on business. I do find it sort of poetic that we all simultaneously said "fuck burnout, we're not burning out" and made a synergetic system of identifying vulnerabilities in the challenges, then multi-threaded patch and exploit creation at DEF CON.

For me, I was with the group in defense, pushing out and reviewing patches, which went pretty well imo.

KoTH was still our weakpoint, we were wrangling with the previous LLM challenge's apparent randomness and at a certain point we collected our L for that. We better diverted our attention towards gaps in our system of exploit development, patch development, and so on.

{{< image src="/images/award_ceremony.jpg"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

Clutching first place on Sunday was a great feeling. We got our hat trick and I'm more than happy to celebrate that. Here are some random funny snippets of the end that I really like:

{{< image src="/images/MMM_activities.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

{{< image src="/images/captain_vie.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

{{< image src="/images/scoreboard_dc32.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

## How does one get better at CTFs to play in DEF CON?

I've seen an uptick in people asking me what curated list of tips are relevant to get oneself to DEF CON. It's a reasonable ask and it's one that I don't have a reasonable answer for. I tried to coalesce some of my thoughts and tips on it into a tweet thread a while ago but after some consolidation of resources, I have a _slightly_ (?) better (??) answer for it.

Fellow CTF player and my good friend, [ZetaTwo](https://zeta-two.com/), said it best: you should learn enough about the other categories of CTFs outside of your main to get to a good enough level to solve those categories at a medium level, whilst you focus on building out the skills needed for your main category. 

It can take some time to learn, depending on the person. If you're someone who doesn't like low-level stuff than binary exploits and reverse engineering require concepts of computer memory and operating systems that will feel alien to you. (Hey good thing there's, like, a free [set of](https://www.hextree.io/) [educational](https://pwn.college/) [materials](https://dreamhack.io/) for that). Or if you're not one for number theory than you're not gonna like the RSA problems in cryptography (Hey good thing there's a free set of [educational](https://cryptopals.com/) [resources so on and so forth](https://cryptohack.org/)). Or if you don't like JavaScript, PHP, server-client paradigms, obscure client-side quirks about the browser, you're not gonna like web (HEY [GOOD](https://picoctf.org/) [THING THERE'S](https://webhacking.kr/)-). My point being, ZetaTwo's advice is solid advice and the one that I try to follow when I learn new things. It's just not a particularly trivial bit of advice to follow. 

(But what good would the advice be if it was easy? Was winning DEF CON easy? No, it wasn't. Nothing worth doing in life comes easy. I didn't bust my ass for near 20 years of my life learning how to best shade in an apple with oil paints to come out of it saying it was easy. I still have callouses around the pads of my palms that gripped the brush, they're never going away. Who says that learning something you love doing would be easy? It's easy when you look back and do the easy challenges, ones that you struggled on previously, or paint the simple things. But I don't wanna paint fuckin apples all day. I don't wanna solve easy XSS `element.innerHTML = "your exact URL query"` all day. I want a challenge because that's where the fun is. Have you ever turned on god mode when you play Skyrim? If you played Skyrim throughout its entirety while on god mode, that shit would get boring really really quick. There's no skill progression, no failing and trying again, no adjusting of strategy, you just one-shot everyone while you T-pose your way to Alduin face-down holding a pot like OKAAAyYY dragonborn that's not fun. Turn off the goddamn god mode. You learn and derive fun from the challenge of it all. Why does everyone like Soulsborne games? EXACTLY. When it's easy for too long it's boring. Elden Ring was so good. 

Oh my god. What was I talking about earlier? CTFs right)

In my opinion, Z2's advice is two-pronged: get used to the concept of "challenging", and also round out your skills to build yourself up. The thing is, if you come across adversary while you learn a new skill, or if you come across challenge you feel like you can't overcome, that's a Really Good Thing actually. I'd make the extremely anecdotal source-I-made-it-the-fuck-up argument here that you need both a consistent reward base (so solving and getting the flag in CTF challenges) and a portion of negative stimuli and failure (_not_ solve a CTF challenge and learn why) to improve your skills. After all, how do you know where to break your ceiling if you don't know where your ceiling is?

Azeria of [AzeriaLabs](https://azeria-labs.com/) has this blog post that I like to look to when I want to Learn A New Thing: [The Process of Mastering a Skill](https://azeria-labs.com/the-process-of-mastering-a-skill/). She goes into good, researched detail about the science behind learning a new skill (go and read the whole article. It's really good):

> Today we know that it’s not magic. It’s this specific form of practice. If you look at the so-called “geniuses” throughout history, time and again the common factor is not innate talent, but just that they each put countless hours into mastering their craft long before arriving at their respective breakthroughs.

As an aside, people often praise my artistic capabilities as talent. Like, okay? We can make the argument that I expressed a desire to draw and doodle at a very young age - that could be defined as talent. But talent doesn't explain the progression of the toddler's doodle to now. So over the course of 2 decades (I can't stress this enough I spent 20 years of my life learning this craft, 20 YEARS like you'd think I would be drawing Baroque realism all over the place but no instead I fight computers for a living) I went to art classes, got tutoring, did lessons, and practiced art on my off time outside of my schoolwork. Talent got me my first step and consistent habit got me the rest of the fuckin way there. 

So, am I going to spend the next couple of mins of this post evangelizing grind culture and HaRD WOrk? No, not really, either. But I am going to state a pattern I'm observing:

Here is a [post](/posts/2020/08/googlectf-2020-pasteurize/) that I made at the beginning of my CTF journey. It's, TL;DR, an XSS problem which uses quirks in the qs parsing library to bypass XSS sanitization. I think I spent the first 24 hours of GoogleCTF on it? I didn't solve the other one, cause it was beyond my capability. 

Here's a [GoogleCTF problem](https://github.com/google/google-ctf/tree/main/2023/quals/web-vegsoda) I made 3 years later about employing type-confusions to chain a series of class functions together to bypass XSS sanitization alongside a CSRF bypass with different HTTP methods. Notice how the challenge from 2020 involves the same general point of XSS sanitization - except now I actually knew what I was talking about in the year I made my challenge. Pasteurize, the challenge from 2020, taught me very important concepts of XSS sanitization, type confusions, and parsing differentials, ALL 3 things that were very useful during my development of Veggie Soda. I couldn't have done Veggie Soda without struggling through Pasteurize.

From the years 2020 - 2023 I wasn't doing fuck-all, I was learning. I learned alot of parsing differentials and XSS sanitization that day in GoogleCTF 2020. But at that point, Pasteurize was the hardest challenge I knew at the time. In the 3 years of progression I learned about different things through different [challenges](https://jamvie.net/posts/2020/11/dragonctf-2020-harmony-chat/) that [confronted me](https://jamvie.net/posts/2021/10/asis-quals-2021-lovely-nonce/) at [the goalpost I was](https://jamvie.net/posts/2022/08/defcon-30-retrospective/) and only [allowed me a solve](https://jamvie.net/posts/2023/08/defcon-31-retrospective/) if I ventured out towards the next goalpost.

### So if I hyperfocus on it, I can get to DEF CON too?

Hm.

Here's another anecdote: At UBC, the computer science major has gotten so popular as of late that entry to the major is competitive. At UBC, you apply to join a faculty (Arts or Science, if you want CS). After your first year, you declare a major. For most majors, it's simple enough to declare it and get on with picking your requisite classes - but for certain majors, you need to apply and see if you'll get accepted against the general average. This general average is the average of the other students applying for the major - if you're above that average you're likely to get in. If you're below, well.

Over the years this competitive average has gotten higher and higher. And the concentration of students interested in computer science is higher than it was when I started. I see why a competitive average was needed. Alot of good schools in Canada employ similar strategies to optimize for the student that they feel can succeed in their computer science programs. I can imagine that, while not exactly the same, the barrier of entry to computer science is similarly hard for universities across the world. It makes sense.

I also see it yield a very particular environment for a particular type of Extremely Anxious, Hyperfocused Student. If you want a good chance at getting into the CS major, you need good grades, obviously. If you want to really optimize your chances than you're probably going to look for the courses that are "easy" to take but still relevant to what you're going to study for in CS. This means enrolling in the courses that are the least amount of work needed to get the highest grade possible - for example, you tend to see many CS students at UBC take Philosophy 220, because it covers much of the same ground as Computer Science 121. Both courses cover logic, just under different pretenses. A CPSC 121 student won't really be covering new ground in PHIL 220 and vice-versa. 

In your first year, if you want to get into CPSC, you need to get the high grades. It's """"""easy"""""" to get high grades if you take """""""""easy"""""""" and **relevant** courses. And the cycle sorta continues. If you want to participate in the Science or Arts CS co-op program, you need a specific average. So you will continue minmaxxing for high grades on """"""""""""easy"""""""""""" and **relevant** courses. This means that you get really good at taking tests and studying for specific concepts in material. It's good to get the full picture but it's efficient to read out the paragraphs that explain the specific problem you're trying to solve.

At a certain point, the material for upper-level courses shifts. The general expectation of the CS student at this point is to instead focus on a skillset that they feel most useful to them. In your 4th year, you're not going to have the time nor energy to be able to try all the **relevant** courses anymore, and also, _fucking most of them are NOT_ """"""""""""""""""""""""easy"""""""""""""""""""""""". Maybe some of them are but that is a more subjective take over anything else. 

Alot of CPSC students will happily reccommend their favourite upper-level courses and often use words to describe the course's merits on how interesting it is, how dynamic it is, how relevant it is to the world, how much they like the professor, or how unique and cool the material is. You will still see some comments on the general difficulty of the course but that goes into lesser relevance over raving about how cool they thought the course material was. 

You're still going to have to take tests in your upper-level courses duh, but this time, UBC is hoping you've chosen the upper-level courses that you don't mind going the extra mile for. The courses that, you know, you would read more into the textbook for. I don't think that's a hard ask of a 4th year student to choose the courses that they actually want to delve into the material for. UBC is hoping that, alongside all of those skills you developed minmaxing for high grades on """""""""""""""""""""""""""""""""easy""""""""""""""""""""""""""""""""" courses, you also developed skills of curiosity and a healthy respect for the desire to develop a robust skillset. Let me be clear: that is a mindset you should have been developing on your own, outside of class. This is relevant because the real world cares less and less about your GPA minmaxxing ratio. It cares if you have the ability to solve problems you've never seen before, with skills you built from twisting material you learn into new scenarios for your own curiosity. The real world is not a standardized test.

So if you "study" for DEF CON like a standardized test, I think you'll get much of the base points _in theory_. But in practice, DEF CON isn't a standardized test. And neither was Hackceler8. And neither was my interview process. And neither is my job. The generalized skill of learning how to take a test is a different thing over learning how to build your skills that allow you to succeed in intense events (as a side note can you imagine if there was standardized test taking as a weird olympic sport? Oh my GOD all my "gifted as a child" peers RISE UP).

You need to build skills that reward curiosity and are rewarded by active and consistent question-asking, ambition, and a genuine pursuit for knowledge. You need to build skills that have you look at the unknown with excitement and apprehension - not giving up at first sight of adversary. My colleagues say it best: if hacking was easy, **everyone would do it**.

Anyway. In relation to qualifying for big events like Hackceler8, DEF CON, SECCON, the proof is in the pudding - you put consistent effort in the things that you are genuinely curious about. You have to broaden your horizons by getting dirty into the material you want to earnestly learn. You dont just get there by solving a CTF challenge and checking the box, you get there by deeply investing in the topics that the CTF challenge demands you to understand. If you have solved an XSS sanitization bypass, I expect you to be able to tell me more about the web API standards, DOMpurify, and XSS DOM sinks and sources, or even beyond: service workers, event handlers, server-side XSS - if you actually marinated with the challenge. And if you're not genuinely curious about it, it will be hard to scratch up the will and determination to keep learning.

You don't _just_ learn reverse engineering, you're also learning things like rapidly prototyping environments to replicate the vulnerability you want to exploit. You don't _just_ learn web exploits, you're also learning how different web APIs in the window interact with and potentially fuck up security boundaries. My advice, summed up, is to stand on business. You're not just learning about how to solve a CTF challenge, you're learning about all the concepts that lead up to it. _Be curious_. Explore and answer the questions that pop up in your head, on your own. Don't be afraid to be wrong. Don't be afraid to tackle a challenge you don't know anything about. Just understand that investing in your curiosity and delving deeper into your own interests is, like any investment, one that takes time. Spend less time trying to learn things passively, or going over material you have mastered already. We're not trying to solve the same kind of problem here, nor are we studying for a test - you don't need to memorize definitions exactly to get points. Start getting into this habit: **Try a CTF challenge, and don't even consider looking at a writeup until you have deconstructed every piece of code in the challenge**. Ask ALL of the questions: "what is this API? What does this keyword mean? What is this? What is that? What happens if I do this?" - if your brain asks the question, your brain commits the answer to memory alot easier. As someone who spent 2 decades (**20 FUCKING YEARS OF MY LIFE**), I should know. Good things take time, and time rewards curiosity. Time is also consistent, so you should be too.

## Cool, anything else?

Uhh. I did Hackceler8 2024 commentary and game design again. That was really fun. Here's a few photos from Spain (where Hackceler8 took place this year):

{{< image src="/images/malaga_cathedral.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

{{< image src="/images/MALAGA.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

{{< image src="/images/HCL8.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

I really do like documenting the things I did in the wake of a shit year because it does remind me that 2024 was still a good year of growth. Here are some of my favorite things from 2024:

* MMM hat trick at DEF CON, winning the last 3 consecutively
* Making and expanding upon Hackceler8 2024, which is a feat I can't claim full credit for, I am very thankful to be working with a talented group of literally the best security people I have ever met
* Kendrick won his Drake v. Kendrick beef
* I did a talk at Area41 talking about how an artist became a hacker which covers alot of the points I talked about here already

## New Year's Resolutions

ᶦᵈᵏ ᵈᵘᵈᵉ ᶦ ᵏᶦⁿᵈᵃ ʲᵘˢᵗ ʷᵃⁿᵗ ᵃ ᵇʳᵉᵃᵏ

I'd like to be more proactive about some of the projects that I wasn't able to keep track of this year. I set the pathway stones down for some things I wanted to do for a really long time, so I'm treating 2024 as a filler arc and hoping 2025 I actually do remember who the fuck I am and get to achieving some milestones. 

I said earlier that I spent most of my time becoming an artist completely on my own. I don't think I'd go back to dedicating more years to it, but I do think I would be happy to spend a little more time in the CTF community, probably because I have great people around me.

Maybe that's my advice. Just, like, find a really cool team of really cool people who are not just your CTF teammates but are also your friends. I think that helps a lot. 

Anyway, goodbye 2024.