---
title: "DEFCON 33: Retrospective"
date: 2025-08-11T16:30:33-07:00
draft: false
toc: true
images:
tags:
  - untagged
categories:
  - retrospectives 
---

1 CTF. 2 full days of hacking. 3 teams. 4 victories. 

{{< image src="/images/DC2025_MMM.JPG"  position="center" style="border-radius: 8px; height: 75%; width: 75%;" >}}

When we won last year, our hat-trick, we were the first in DEF CON'S history to do it. 3 consecutive victories, 8x3 (24) black badges awarded in 3 total years. I think it was safe to say that a reputation formed around us and naturally, speculation. PPP and The Duck already had their own reputations so it's not like the secrets and rumors and awe towards MMM came out of nowhere. However, it's not like MMM was the only team that did well enough to have a reputation in the CTF community.

Any team that has managed to qualify for DEF CON is a strong and worthy team. Each one with decorated legacies and reputations, formed from other victories and vulnerabilities found. Every team had their powerhouses, and none of them were discounted. We didn't have a notion of a "weak" team, here in MMM. And as such, we treated each and every one of our competitors with their respect that is due. This also meant that the concept of "going easy" is not in our lexicon, and never will be. Each team we encountered in DEF CON was a worthy and powerful rival. So we didn't hold back. My opinion piece on DEF CON came a little late, probably later than it could have to influence some people's opinions on this matter. But, I think after about 5 years of CTF, and idk how many more to count, I'll admit I gave up on the idea of ensuring that I've impressed every single CTFer I've met in my life. I worked hard. I played hard. Discount that how you will.

## PREGAME

I arrived on the Wednesday, August 6th, to try and at least enjoy Vegas a little bit since the consecutive 5 days after will give me no relief or reprieve. So, I don't really have a lot of technical stuff to discuss here, so I'll indulge and spend some time talking about the things I did. 

First, I met up with a teammate and we spent the whole day together, getting oriented around Vegas after an early room check-in. I got tacos and tequila at Chayo's near the Vegas strip, then picked up some other teammates who also came early and hung out with them for the rest of the night. It was really sad - Chayo's has a mechanical bull in the back that I really wanted to go on but it was closed down that day :(

{{< image src="/images/dc2025_chayo.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

Thursday was when most others were in Vegas, and I decided to get some breakfast with teammate at the Paris hotel at Mon Ami. The rest of Thursday was prep. We set up our tooling, debriefed on what each and every team member's roles and responsibilities would be, and coordinated other logistics.

This was our 4th year coming to DEFCON, and we held no reservations. Our last win last year was hard-fought and we expected a harder fight with more aggressive teams. The Thursday debrief set the scene to ensure that regardless of what the outcome was, we would give it our all.

## Day One - Friday

{{< image src="/images/dc2025.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

DEF CON's usual light banner, this time in a giant E. 

OH 3 FOR 33

{{< image src="/images/dc2025_area.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

Friday I was at the floor, and had a fun time trying to figure out networking logistics. At the beginning of the game there seemed to be some ethernet cable issue with our setup that thankfully, a bunch of the organizers came to try and help with. I think we lost precious minutes at the start of the game but the rest of the days forward made up for that.

Nautilus Institute's last organized DEF CON introduced a (new to me, old to others in MMM) mechanic called "stealth ports". Each challenge, on each player's box, had 2 ports running the same service. Port A's traffic was recorded and provided to us in pcaps. Port B's wasn't. The flags sourced from port A were of a very high point weight than that of port B. You therefore had an interesting metagame with stealth ports: you could hide a potentially valuable exploit by hitting teams through stealth ports but lose a SIGNIFICANT amount of points (75% decrease), or you could expose your exploit and allow others to potentially copy it from the exploit, but gain full points that way. This introduced a very welcome layer of strategy onto us that we tried to use throughout the game.

Aside from disappearing wifi and new meta discussions of stealth ports, I also had the opportunity to enjoy some challenges - one of which was the first King of the Hill: Attestedfun. 

### KOTH: attestedfun

{{< image src="/images/dc2025_af_phone.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

The first KOTH challenge, attestedfun, was very interesting. I don't think I've ever had a challenge that involved a phsyical phone for interactions with, so Nautilus Institute takes the cake for this one.

The premise was simple, after reverse-engineering a server you're given (on the floor) a physical android phone with nothing on it (factory reset). You use it to interact with an app that's just a text box on a green screen. Typing anything in it forced you to watch (at 90 volume cause yall are SICK) either steamboat willie or rave gandalf in its entirety. When the troll was over, the app spat back a seemingly base64-encoded string to you. 

After I recieved the phone on the floor, I set it up and downloaded the companion APK. In case there was anything special needed for the phone, I got a few measures to add frida into the app for dynamic instrumentation, and at one point we rooted the phone, but reversed it when we realized we didn't need to. 

The actual meat of the challenge was in the rust server. It was a gigantic binary that we manually reversed, and some portions of it are still confusing to decipher. Anyway, in the first iteration of this challenge, there were the following problems: 

```
b1
biosig1
biochain1
biochain2
biochain3
biocert1
biocert2
biocert3
biochall1
biobstate1
pcsig1
pcsig2
pcchain1
pcchain2
pcchain3
pccert1
pccert2
pccert3
pcchall1
pcbstate1
di0
di1
di2
di3
di4
```

{{< image src="/images/dc2025_af_app.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

The rough idea of attestedfun was to reverse the logic behind each challenge and perform the requisite actions to satisfy them, on the phone. I could go into greater detail into a seperate blog post about this, as attributes of it were really cool. What wasn't cool was having to, intermittently every 5ish mins, hear steamboat goddamn shit-ass willie in its entirety for the rest of the game. I stand before Christ with how close I was (very close) to losing my shit (very very close).

{{< image src="/images/dc2025_af_app_2.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

### End Day One

{{< image src="/images/dc2025_sleep.JPG"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

Conclusion of day 1 ended with us on top, with a steady lead. However, we've been through this 3 times already. We knew that first looks are decieving. In spite of our lead we didn't slow down - the next night would be sleepless for most of us.

{{< image src="/images/dc2025_yeet.JPG"  position="center" style="border-radius: 8px; height: 100%; width: 100%;" >}}

## Day Two - Saturday

Saturday was business as usual! We woke up, most of us, and span our exploits found overnight ready into our thrower. While we started with a lead, we also knew how easy it would have been to end without one. Saturday was also the day I focused on and looked at 2 additional challenges: hs and axis. 

### KOTH: hs

I was not a fan of this KoTH, my main feedback being that it doesn't "look" like a KoTH.

My understanding of a King of The Hill challenge was that of one with a moving goalpost in relation to other teams. You are therefore forced to perform iterative optimizations reactionary to what other optimizations other teams are doing, to maintain a top position. This is why KoTH challs have their own scoreboard to measure these nuances in said optimizations. 

The first problem I had with this KoTH was the ***lack*** of a scoreboard. It is unclear to me if there was supposed to be one in the first place, or if it was added on later, but regardless the initial few hours that hs was live it was totally unknown to teams how we were doing in relation to each other. This felt like an important aspect of any KoTH that wasn't reflected here.

HOWEVER, one could make the argument that a scoreboard wasn't necessarily needed as once you solved a prompt/got a flag, you were done for the rest of the challenge, which brings me to my 2nd problem: there was no "optimization" to be had.

A KoTH should be able to provide some active, engaging set of "tasks" to do per tick. Once you found the flags in hs, you didn't have to do anything else. You could passively gain points after figuring out 1 needed prompt for 1 needed flag. This removes some of the properties of what makes a KoTH so great. Hs felt more like a jeopardy challenge shoehorned into DEF CON, over a challenge specifically tailored to accomodate consistent tinkering upon optimizations for the problem. I think this would have landed better as a jeopardy challenge during quals, or maybe an attack-defense challenge with tweaks, but it just didn't make sense as a KoTH challenge. We dedicated minimal resources to this as the ROI on this challenge was tiny, and dwindling faster than other challenges. Our priority landed on those other challenges.

### AD: axis

Axis was a web challenge written in erlang, but provisioned as an erlang binary so we needed to reverse it. The reversing was probably the most interesting part, as the web component here was more straightforward. The code of Axis was recycled from previous challenges: [rawwater](https://github.com/Nautilus-Institute/quals-2023/tree/main/rawwater) from the 2023 quals and [gilroy](https://github.com/Nautilus-Institute/quals-2024/tree/5e15c5e1a2cb100f86c22e759536327e23af5bf8/gilroy) from the 2024 quals. Snippets of the reversed binary revealed dead source code that served as a sort of template from both challenges that came before. Some of these dead code bits created rabbit holes, but we did have a jumping off point to base some of our exploits on. Most of said exploits were SQLi, as the challenge allowed you to modify fields in other tables of the SQL database: `vie', name = (select flag from flags) || '`. Creating forms also allowed you to view these table fields for flag viewing purposes. 

### End Day Two

We got kicked out of LiveCTF pretty early so our point delta would look different without the boost of points. This year's stealth ports meant that every other point-gaining attribute got a massive scale up, so losing out of LiveCTF top 3 meant that our lead wasn't as big as we thought it was. This was concerning - the delta between first and second place was smaller than we thought. Furthermore, the last day of Sunday was, idk, 3 hours long? We couldn't spend those remaining hours doing fuck-all. Last night, and last few years, we encouraged and had a system of people who slept while others worked, on shift. This day, we all collectively said fuck it. All of us were up. 

## Day Three - Sunday

I don't have much to say here since the day lasted 3 hours - basically. I guess I have more to say on the afterparty, but that comes later.

We concluded Sunday throwing everything we got. Fuck stealth ports - just throw whatever we had. For the last bit of the game lasting 3 hours, it admittedly was pretty intense. Bpak got the call for our victory and we were requested for our 8 black badge recipients. On MB's side, we chose some people who showed an intense dedication during the game itself, and to the CTF team as a whole. 

{{< image src="/images/dc2025_awardsceremony.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

The black badges this year were cool - the lanyard was an intricate hand-beaded design with a brass cast of this year's design.

{{< image src="/images/dc2025_blackbadge.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

### Afterparty

Unlike previous years, this year's afterparty took place in a (?) compound (??) of multiple houses with a general backyard area (???) centering them. The afterparty was, as usual, very fun. I spent most of my time with my teammates but it was genuinely lovely to see all of my other CTF friends there too. The party is the ONE time I get to have to see alot of said friends around, as I'm not really venturing around meeting other CTF players during active competition times. A lovely shoutout to some cool people I met from mhackeroni, KuK Hofhackerei, and of course Dicegang (SuperDiceCode) and Shellphish. 

{{< image src="/images/bluepichu_vie_yeehaw.JPEG"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}
*Bluepichu and Vie say yeehaw*

## To Nautilus Institute

4 years. I understand that for some of you, this was not your first rodeo. 

Your breakthrough year of 2022 was controversial, to say the least. Many CTF teams have various thoughts about how DEF CON was organized during your reign. I won't discount the valid cirticism that they may hold, BUT I will ignore the less-valid criticism that were thinly-veiled insults, that shit isn't productive. In fact, I'm sure you've heard it all already. Many teams, especially the ones who played in the CTF this year, have also organized their own CTFs, so some points of feedback are coming from wiser places than others. 

But every CTF is different. And I also know (in fact, perhaps am very aware) that you recieved 10 complaints to every 1 compliment directed at you. You've shouldered the task of a fucking **thankless** job for 4 years. Everyone wants DEF CON CTF, and nobody wants to organize it. It's as if we all implicitly know that if we'd take up the mantle we'd open ourselves to more hate than love, because teams clamor up and desperately fight with their fangs out during quals for a chance to hit Vegas. We all _think_ we'd do a better job, but there will always be something to hate. You have run _the_ CTF, the CTF that has the allure of a siren to the community. Everyone has opinions on it.

I've been on the other side as well. Organizing a big, high-stakes CTF means you're given an ocean of responsibility that's easy to drown in. I won't absolve you of mistakes you may have made, but I know I don't have even the smallest shred of understanding of the ocean you were in anyway - *and I help with GoogleCTF*, a similarly gigantic high-stakes CTF.

To Nautilus Institute, I see you. I see the work you've put in, and know that the only thing that was propelling DEF CON CTF forward was the absolute dedication to the craft that could have only come from a principled love for the hacking community. It takes some real guts to stand tall in spite of getting shit on for a CTF you feel like you've dedicated actual years of your life to, even if the criticism was valid (yes, I have valid feedback, and I hope I have communicated that effectively earlier). I know what its like to sacrifice your nights to keep a server running and then ignore the hateful messages *in the same hour*, I commend you for the ironclad guard you would have needed to withstand that. That kind of determination and drive to push yourself (even when you might kill yourself doing so) comes very rarely, and is worth its weight in gold. I've been in your shoes. I see you.

Goodbye, thank you for the blood and sweat and tears that created 4 years worth of hacking, and goodluck to the next organizer.

## Closing Photos

{{< image src="/images/dc2025_sharkjason.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

Jason (Kirkland Green Tea) packed a shark costume and wore it for our team photo.

<br>

{{< image src="/images/vieboatwillie.png"  position="center" style="border-radius: 8px; height: 100%; width: 100%;" >}}

Vie Boat Willie

<br>

{{< image src="/images/dc2025_mbshirt.png"  position="center" style="border-radius: 8px; height: 50%; width: 50%;" >}}

We got Maple Bacon custom shirts :)

<br>