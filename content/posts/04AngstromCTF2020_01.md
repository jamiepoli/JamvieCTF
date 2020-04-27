---
title: "AngstromCTF 2020: UBI"
date: 2020-04-26T01:30:30-06:00
draft: true
feature_image: https://cdn.hausarzt.digital/wp-content/uploads/2019/07/AdobeStock_239849231_burnout_nadia_snopek_jpeg-lowres-150dpi-rgb-1000x1000.jpg
---

This challenge was easily one of the most complex ones I came across. It was so complex, in fact, I didn't end up solving it during the CTF, I had finished it 3 days after.

<!--more-->

Have you ever heard of appcache poisoning?

I guess, the first thing to ask, is do you know about the cache manifest in HTML5?

the tl;dr of it is that your appcache dramatically imrpoves the performance of your browser by caching your most recently accessed file pages, predicated on the assumption that you'll most likely visit those sites again in the near future. It's the same ruleset as any other cache example in computer science, like in operating systems, the cache will keep a copy of any relevant data from memory that you are known to constantly access. So, instead of constantly returning to the same server/same place in memory, your cache just serves up the data instead, saving you the trip. 

The cache manifest lets you access webpages even without a network connection, provided you visited them recently when you had one. 

The idea here is to copy the relevant addresses into a manifest file, and so if you're offline, the browser only needs to check out this manifest file and rebuild the site with the metadata in it.

Typically, your manifest file might look something like this:

```
<html.manifest = cache.manifest>
CACHE MANIFEST 
    /stylesheet.css
    /index.html
    /test.js
    /test.png
```

So with that being said, one can possibly visit a server with malicious payloads in their manifest file that the cache will then save, and serve to others who use the program/client. 

TODO: Finish

And that is the exploit of UBI in its entirety.

_incoherent screaming_

Jam