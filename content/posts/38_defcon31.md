---
title: "DEFCON 31: Retrospective"
date: 2023-08-31T00:00:00-00:00
draft: false
toc: true
images: ["images/MMM_2023.jpg"]
tags:
  - untagged
categories:
  - retrospectives 
---


## Beginning

Back in October of 2019, I was attending my 3rd/4th? year at UBC, where I tried to play in a beginner-friendly CTF known as "CSAW 2019" with UBC's team: Maple Bacon. Particularly, I tried out a challenge called Unagi, which, having spent an eternity on, I managed to solve after hours of wrangling with the problem. 

To be completely frank, I solved the challenge after googling "XML vulnerability" and trying out payloads - really, just, throwing payloads and modifying them to tailor the challenge - until one stuck and I got the flag. I was happy, but I also thought, "what the _hell_ did I achieve? what did the payload do? What did I learn?" and I couldn't come up with an answer I was satisfied with. I really just googled information about XML vulenrabilities, found an XXE payload, and threw it hoping to see if something would change (I didn't even know what XXE stood for). Granted, I still needed to modify the payload. Unagi required more effort than just a copy-paste. But overall, my first experience was largely juvenile and I was still as naive as I was pre CSAW 2019. 

I remained curious in CTFs the following year, but didn't really play in them as much until I was complaining to a friend of mine about something entirely different - Super Smash Bros. I really liked the game, I was playing in it so much I was considering playing it competitively (nothing too serious really came out of that) but I wanted something else to satisfy my competitive nature. So my friend suggested DEF CON. 

> V: "Like the alarm codes?"

> L: "Okay, _yeah_, but I'm talking about the hacking competition."

> V: "Hacking? Yea, like CTFs? I'm familiar."

> L: "Uh huh, like the movie _Hackers_. You know, the one with Angelina Jolie?"

> V: "Wait, Angelina Jolie was in a movie called _Hackers_?" 

> L: "You're missing the point. Have you ever considered CTFs to help with your competitive nature?" 

I knew the CTF scene, not that well, but I knew it. I played in a CTF with MB, and I thought about what my friend said. I figured, sure, why not? 

I attended my first few meetings with them, exploring CTFs, starting with PicoCTF under prof. Robert Xiao's advice. Pico was considerably easier than CSAW, generous with hints and well-scoped for people to learn something new in security. I took my time, taking a couple days to solve all. The series of SQLi challenges, when I came across them, gave me a challenge at the time. But when I solved them, I was elated - happy, cause submitting a flag felt really, really good. 

Debriefing on the Pico challenges after submitting all of the web ones, Robert Xiao talked about DEF CON. I had minor context from my own research and from what my friend told me, but hearing him talk about it and all the cool challenges he saw was inspiring. DEF CON CTF seemed interesting, challenging, but importantly - _fun_. I wanted to participate. 

Recall that at the time that I resolved to participate, I barely knew what an XXE was, nor did I play in any harder challenges outside of Pico and one (1) CSAW challenge. So, was the goal of DEF CON attainable? Not anytime soon at that point in time. I gave myself a comfortable margin of 7 years. If I could be in Vegas for DEF CON 7 years from then (2020), I'd be set.

That was 3 years ago. 


{{< image src="/images/MMM_2023.jpg" alt="MMM" position="center" style="border-radius: 8px;" >}}


## Maple Bacon

{{< image src="/images/DEFCON31_MB.jpeg" alt="MMM" position="center" style="border-radius: 8px;" >}}

I'd really like to first explain some things pre-DEFCON 31 Finals. You may notice during the quals, MMM wasn't there - Parliament of Ducks was. That was actually **our, Maple Bacon's,** decision. 

I'm not dumb. I knew that last year, the announcement of the merge between The Duck, PPP and us was a shock, simply because 1/3rd of that merge was not as highly-regarded as the other two. I know that. PPP has several different championships under their name, and Theori (The Duck) is a security powerhouse, an extension of one of PPP's co-founders Bpak. So what was Maple Bacon doing there? At the time of DEF CON 30 I was the leader of Maple Bacon, and like I said, I'm not dumb. I knew what others thought of the merge. I knew that Maple Bacon came into their first DEF CON with 0 prior DEF CON wins. I knew people would talk. But I was the leader. So I also knew that my team was determined, ambitious, and capable. They were nervous, maybe a little green, but I saw all of their progress and development, ever since the _inception of the team_. **I was there for all of it**. I knew they were _good_. And clearly, so did PPP and The Duck. 

Sorry for the soap opera paragraph. Back to the original question.

We were the unlikeliest team to show up in said merge. The answer, given the context, is simple. We wanted to be taken seriously (yes, even after DEF CON 30 victory). And during quals, we wanted to show that we were just as ready for DEF CON as others. We had less than 15 people play during quals. It sucks that we missed the qualifying threshold, but for a very brief moment, I think we made other teams do a double take for a good portion of those quals. If other teams had, at one point, felt a brief but palpable shock at our existence on the scoreboard, that's a success for me. Doing our best with far less people than many other teams, and having that reflected in our quals performance, felt pretty good.

Since I graduated from UBC, Maple Bacon has transferred into new leadership. I'm certainly still involved but in a much smaller capacity. However, I must've done something alright if they're still going strong today. DEF CON isn't their only win, and it isn't the only big CTF they've done incredibly well in. Like I said, I know my team, and I see with my own eyes the sights they're setting and the hard work they're putting in. They've earned the respect they have recieved.

## DEFCON: PREP

Thursday, prep day. I arrived in Vegas a little earlier, and managed to get quite a few things accomplished. I met some old friends and new people, from co-workers to other CTF players I really admire and respect. Synced up with my team for the night, and got everything - tooling, roles, infrastructure setup - organized. 

Later that night I went to the Chandelier bar at the Cosmopolitan hotel, and had the _Secret Menu_ drink called the "Verbena", which, *IFYKYK*, and oh my god I did not and what a shock to my senses that was. 

{{< image src="/images/Chandelier.jpg" alt="MMM" position="center" style="border-radius: 8px;" >}}
_At least the bar was pretty though_

## DEFCON: DAY I

{{< image src="/images/DC31_Banner.png" alt="MMM" position="center" style="border-radius: 8px;" >}}

I was on the floor the first day and helped set things up. Last year, I was expecting a similar setup of round tables in a single room, so it was quite a nice surprise to see a far larger area for us, with nicer setups for each team. I think others have echoed similar sentiments but the CTF gave you 0 free time to check out the rest of DEF CON, which is a damn shame cause I really wanted to see some of my co-workers over at AI Village. Cest la' Vie. 

## DEFCON: DAY II

{{< image src="/images/vieodore.jpg" alt="MMM" position="center" style="border-radius: 8px;" >}}
_In case you forget our names._

After the network closed on day 1 we were informed of the fact that no challenges will be retired for the rest of the CTF. This was a pretty significant change to the meta, since we would have to keep looking for deeper bugs in day 1 chals while also balancing looking for bugs for future challenges. The night between day 1 and day 2 was a sleepless one for most of us, and very surprisingly I found myself busy with a web challenge released towards the end of day 1. 

Day 2 starts and we started coming across connectivity issues, which was later revealed to be container misconfigurations that affected several other teams. While frustrating, I think we're all used to testing exploits locally so luckily that didn't affect our workflow too much. Besides rationing connections, a bunch more challenges were released and the ennui of it all was definitely starting to settle in. While we managed to have all challenges accounted for, the balancing act definitely had its effects on us. 

## DEFCON: DAY III


{{< image src="/images/MMM_braincell.png" alt="MMM" position="center" style="border-radius: 8px;" >}}

{{< image src="/images/pie_flavor.png" alt="MMM" position="center" style="border-radius: 8px;" >}}
_DEF CON DAY 3 :]_

About 6-ish hours of sleep (which is ALOT for DEF CON days) later, day 3 starts and I was dual-wielding patch-busting and LLM prompt injection, which is a brand new sentence if I've ever seen one. There was an LLM KotH challenge that had players submit attack and defense prompts to get other team's LLMs to spit out a provided secret. It's a nice challenge, certainly applicable to the landscape of AI today. 

Near the end of day 3 we were mostly fixated on the last LiveCTF match between us (Jinmo) and Hypeboy, and it was nerve-wracking, to say the least. It was a race against time to figure out why the exploit wasn't working remotely even though it was fine locally, which we later discover was a network hiccup - anyway, the match continued into sudden death, with Jinmo clutching and securing us LiveCTF 1st place. 

{{< image src="/images/jinmo_mmm.png" alt="MMM" position="center" style="border-radius: 8px;" >}}
_Creds to Zaratec_


{{< image src="/images/DC31_scoreboard.png" alt="MMM" position="center" style="border-radius: 8px;" >}}

What a beautiful scoreboard. 

### AWARDS CEREMONY

{{< image src="/images/dc31_awards_ceremony.jpg" alt="MMM" position="center" style="border-radius: 8px;" >}}

Getting a black badge feels surreal. It feels additionally more surreal thinking about where I started and how I got here. I spent a significant part of my life catching up to people I admired. I used to have my future set for an artistic career and I perpetually felt in the shadow of other artists I learned from. All throughout college I was insecure of the fact that I had 0 direction nor pre-requisite coding ability compared to my peers, and felt like I had to spend my free time learning what the fuck multi-threading was just to feel normal around my friends. It's the same feeling in the CTF scene. Alot of people have, and probably will, doubt my ability. I was happy about learning what SQLi stood for around the same time others in the scene were doing much, much more. And I'll be honest, I'm not sure I'm done "catching up". But at the very least, I don't feel like I have to anymore. I don't feel the same impulses to compare myself against others. I want to "catch-up" because there's still a world of vulnerabilities I would love to learn about, for myself.

## Epilogue

I remember in the later part of 2020, around November, Maple Bacon and I played in DragonCTF 2020. At that point in time just several months ago I had graduated from PicoCTF to regular CTFs, but DragonCTF was a significant leap of difficulty then what I was used to. I wasn't really expecting to solve anything in DragonCTF 2020, but then I did. [Harmony Chat](https://maplebacon.org/2020/11/dragonctf2020-harmony_chat/), which still holds a special place in my heart. I remember thinking "Wow. Just a few months and I've learnt so much. What else will I be able to achieve in the CTF space?"

3 years ago, I learnt about the world of CTFs, trying out problems in PicoCTF and learning stuff about SQLi, XSS, simple stack bofs, and the like. 

This year, I claimed a black badge at DEF CON 31. I authored multiple web challenges, covering topics from TLS poisoning to TypeScript pop chains. I'm a security engineer. I've upgraded from XSS bugs to things like complex v8 speculative optimizational vulnerabilities, novel SSRF techniques, and so much more. 

I'm quite happy with the progress I've made so far. I'm quite happy with the progress that Maple Bacon has made so far. I love my team, Maple Bacon and MMM, no matter how cheesy that sounds. Looking towards the future has never been so bright. 