---
title: "A case for documentation"
date: 2024-01-06T22:23:05-05:00
draft: false
toc: false
images:
tags:
  - untagged
categories:
  - creative 
---


_I am aware that the main target audience of my tiny blog are CTF players looking for my writeups and other like-minded security researchers exploring topics I like. But my 2024 resolution was to relive and unite my artistic persona that I spent most of my teen years curating, and marrying that to the security career life I live now. Part of my fine arts training involved a good deal of literature. I wanted to write some random stuff really badly, and this post came out of that desire, I understand it's out of left field especially given the content of my other work here. If people like this I'd be happy to intersperse more of this into my usual stuff of writeups and security research._


## Leaving behind a paper trail makes it easy to backtrack and review! 

When you have documentation in your projects, if you make a mistake - you can easily review what went wrong by referring to the notes you've made. This makes it easy to backtrack and come up with a solution to fix mistakes if they come, improving developer experience!

In Canadian grade school, I was a stereotypical art kid and spent a good amount of time during any creative projects in class going _all in_. There was an artistic project that required us to recreate a scene from a book we were reading, and I happened to doodle some sketches related to the scene I wanted to draw. 

Another kid saw those sketches and complemented me. I said thank you. I asked for permission to go to the bathroom and when I came back, one of those sketches were missing - the kid that complemented me put it into their own work, passing it off as theirs. They were getting compliments about it, about my work, on _their project_. 

_how dare they how dare they how dare they how dare they_. I spent the next couple hours of that class angrily writing my name onto every sketch that I made. I went to the bathroom again to cry out of frustration.

Was this what it meant to work hard in the vicinity of others who didn't? Did it matter if you were passionate, if someone thought of you so beneath them that your work was up for grabs? And who would contest them? Who would challenge them? If not for me realizing their blatant attempt to disregard me in my own work, would anyone really notice? I was awkward and quiet. No one would advocate for me. At 9, I had this adorable spiral into an existential crisis of understanding. 

_I learned about attribution that day in a sad way. Artists, and plenty other people in different disciplines get their work stolen alot more frequently than one would think. There's a soul-crushing and disheartening nature to the sad realization that all the hard work you put in your projects, things you're passionate about, has been taken advantage of. A genuine lack of respect for that effort. I needed to document my ownership of my work. I needed documentation for attribution._


## Sometimes the answer was covered in something you documented, hidden in plain-sight! 

Have you ever answered a question or found a solution to recurring problem, only to come across that problem again and searched frantically for your previous work on it? If you document your projects, it streamlines this process immensely!

...

When I was a kid, I grew up playing videogames. I had a Sony Playstation, the very first generation, and a small CRT that I played on. I played Spyro: Year of The Dragon, extensively. I remember struggling in the tutorial level, when the cheetah character Hunter was teaching you how to hover in order to reach unique areas in the low-poly maps. After grade school would end for the day I would play and eventually figure out how to quickly mash the correct button to hover mid-flight as Spyro, allowing me to progress into the game. I remember being so elated that I drew a doodle of the dragon to celebrate. What I should've done was, maybe, write down the correct button combo to easily achieve the hover, or something, as whenever I would return to the game I would constantly forget how to perform that mechanic and I'd lose game hours over it. I looked over to my drawing. A simple Spyro-lookalike with my name, in big - result of my existential crisis from last situation - scribbled under his wing. I didn't have instructions.

_I have a good memory. It has served me well all throughout academia. But I can't remember everything. No one can. Sometimes, for CTF challenges, I'll forget snippets of code and payloads that would work in other scenarios. I needed some sort of repository to remember these snippets for me. If you ever wrote down the cheat codes to GTA San Andreas on a piece of paper beside your console, you will know what I mean. I needed to document my shortcuts. I can't remember them all._

## Documentation is paying it forward - other people looking at your work will be able to pick it up easily where you left off.

This is especially the case for open-source, where projects are a collaborative effort! If you want to ensure the longevity of your projects, it is good to have documentation so others can easily figure out what your project is about, how it works, and can pick up on ta-

I remember before moving to the States to start my big girl job, staying at my parents house and my mom helping me clean out some of my things. In the basement, my old Playstation One sat in a corner of a wall tangent to the stairs, placed on top of a box. It was still connected to the CRT. I plugged the TV and Playstation onto power, popped open the lid, and saw that the decades old Spyro game was still in the compartment. I closed the disk lid and powered the PS one on. The TV flickered, a brief flash of yellow and blue and red, and the very familiar orange diamond of the Playstation One startup screen appeared. It was glitchy, and faded - the cathode ray tubes were _old_ old, after all - but the logo flickered, and my excited self took the controller in hand. But all it did was flicker - once, twice, then black. I stared at my reflection in the TV, expressionless. Too much time has passed. Either there was an issue with the TV or an issue with the Playstation. I wasn't an expert in either to figure out what went wrong. I placed the controller back on the floor and got up to continue packing. 

---

"Didnt the Spyro games get remade? In 2018?" My friend told me, over lunch one day. "Yea, but it's not enough." I replied.

A: "How so?"

V: "I'm not sure how to describe it. It just _is_."

A: "Appropriately vague."

V: "I know. I just want to play the original game again."

A: "What about emulation?"

V: "I've tried a bunch. None worked super well for me. Idk, maybe I was using them wrong. The instructions and official guides are vague. I need an actual FAQ, or list of instructions, idk, _something_, some actual documentation."


## Documentation makes you a better developer.

I played in a CTF sometime in April of 2020 where I didn't solve the challenge during the competition. At this point, I was about 4 months into the scene so I was well aware of how things worked and just waited around for a writeup to appear. I waited for a bit, until _one_ - eventually - appeared. Happy, I took a read through it. It was written in Chinese, which wasn't an issue, Google Translate is a thing, and the writeup was **amazing** and **informative** and **incredible** and I didn't get it at all. It assumed pre-requisite knowledge of other contexts before explaining the main point of the bug. Things that were probably really obvious to seasoned CTF players, but a complete fucking alien language to me at that time. The writeup didn't commit a sin. I, plainly, just didn't understand its concepts (Chinese notwithstanding). It frustrated me to no end. I sunk hours, of essentially wasted time, into it. Now that a writeup is available, it's literally _too fucking smart for me to get??_ 

I spent the following next couple of days playing with the challenge locally. Relentlessly poking and prodding at it. Adding print statements everywhere. Deleting chunks of code and re-adding them to see what would happen. Copy-pasting documentation of certain areas in the comments so I could refer to it. I solved it days later. I wrote my own writeup for it, a personal one that I haven't shared, that explains the concepts like I'm 5. I thank the original author of the first writeup I saw. I created another one to catch me up to speed if I ever forgot the basics again.

Later, I researched. Blogs of security researchers that I was inspired by, news articles, other writeups of other challenges. I compiled their links. Prettified them and slapped them into my Notion. Created a database of Things. I told myself I would learn faster than what my anxieties were holding against me. I didn't allow myself the opportunity to be confused. 

When I was finished, I played some God of War (2018) to unwind. I noticed that the Spyro remake was on sale in the Playstation store. 

I ended up playing. It was a package of the 3 original Spyro games of the late 90's to early 2000's. I sat down one evening and powered on the Playstation 4. Navigated to the game on the dashboard. I re-learned how to hover. 

This time, I wrote it down.