---
title: "Commonly Used Software 2: Electric Boogaloo"
date: 2021-04-07T23:10:50-06:00
draft: false
comments: false
images:
---

I figured I would make an update to this [post](/posts/2020/04/commonly-used-software/) from long ago, back when I first made this site. It's kinda funny (and a lil cringy) to look back on it now given how I ended up completely deviating from that list and use an entirely new suite of tools. Actually, one year into CTFs and I'd consider my kit to be far smaller than it originally was. Funny how that works, no?

The entire motive behind that post was to give a quick, general overview of the sorts of tools I felt like you should familarize yourself with to plunge into web exploits. This isn't to say that I no longer think that, the software available to us today is incredibly useful as an ethical hacker, but I felt this update important to subtly showcase my growth as a hacker through the tools I use (or don't use).

Now, on to the new list. 

## 1. ngrok

_[Link](https://ngrok.com/)_

I love ngrok. It's easily my most utilized tool. It's great for literally anything, since at its core it's just a tunneler to expose a port on your localhost. Spin up a quick HTTP server and open ngrok on that same port, and now you have a public HTTP server. Start up a netcat listener and use ngrok on TCP mode, and now all you need is a reverse shell payload to be executed on some poor victim's server. The possibilities are endless. 

## 2. Python
_[Link](https://www.python.org/)_

Can I call Python a tool? It's my scripting language of choice. One year ago, my Python skills were pretty barren (I was a staunch believer in C# supremacy. Microsoft, heyy) - now, most if not all of my coding (from scripts to full-fledged programs) are done in Python. It's remarkably easy to create anything with it. ALL of my ctf scripts are in Python, and most of my academic projects are also written in it. Not only that, but Python comes with an incredible library called [pwntools](https://github.com/Gallopsled/pwntools) containing just about everything you could ever want in a CTF-centered package. 

----

Yea. That's it. I still use Wireshark and Burpsuite occasionally, and I naturally use the terminal for all CTFs so I can't escape CURL, but honestly analyzing my usage of tools I realized that I have seriously downsized. What does this mean for me?

Well for one, I'm **not** saying that the software outlined in my previous post are obsolete. They're most definitely not - Burpsuite, for example, is a powerful web pentesting tool with so much functionality that industry professionals use it today. Like I mentioned earlier, I still use Burpsuite, I just don't use it in the same way I did before. I found that earlier on in my CTF journey I relied on external tools a little _too_ much to do the hard work, which isn't the most productive way to learn.

Perhaps the biggest takeaway here is to acknowledge how much I have grown since even a year ago. There's a wealth of information about cybersecurity out there, constantly being added to and amended, and the process of learning never really "completes". But, you can always take a look back and remark at how much you've changed based on the little things that have adapted to accomodate your more experienced lifestyle. I thought that I needed several different tools to employ many different web-based exploits. Realistically, I wasn't using them in a productive manner - hoping that the software that I dumped the brunt work on would help me progress my own skills. What was I learning by allowing a script to automate the effort?

The shrinking of this list is proof that I have become self-sufficient in the way of CTFs. Maybe more confident, too. I'm happy that the progress is evident whenever I take a step back and look at how far I have come. A year's worth of progress can really be alot. 

**Vie**