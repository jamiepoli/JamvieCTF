---
title: "GoogleCTF 2020: All the Little Things, Post-CTF Reflection"
date: 2020-08-24T20:17:24-06:00
draft: true
comments: false
images:
tags:
    - HTML-injection
    - xss
    - web
    - object-poisoning
---

The 2nd challenge I worked on, titled "All the little things". Frustratingly, I didn't solve this challenge. I was about **85%** of the way there! 
<!--more-->This is less of a writeup and instead a discussion on what I did in the problem and a reflection on the 2020 offering of GoogleCTF. 

## Let's Begin!

Right off the bat, clicking on ``/login`` we have a very interesting login page that only takes your username and a profile image sourced as an http link. I spent some time wondering if an xss payload delivery was in order that way but it turned out to be a dead end. 

Logging in as any user lets us access a new endpoint called ``/settings``. Aside from the available options to change your name, profile picture and theme (light or dark), lets inspect the HTML: 

{{< image src="/images/google2020lilthingsdebug.png" alt="Login" position="center" style="border-radius: 8px;" >}}

Theres an interesting comment. Add it to our settings URL and we get an additional DEBUG form! Additionally, an extra script known as ``debug.js`` gets loaded in static: 

```javascript
// Extend user object
function load_debug(user) {
    let debug;
    try {
        debug = JSON.parse(window.name);
    } catch (e) {
        return;
    }

    if (debug instanceof Object) {
        Object.assign(user, debug);
    }

    if(user.verbose){
        console.log(user);
    }

    if(user.showAll){
        document.querySelectorAll('*').forEach(e=>e.classList.add('display-block'));
    }

    if(user.keepDebug){
        document.querySelectorAll('a').forEach(e=>e.href=append_debug(e.href));
    }else{
        document.querySelectorAll('a').forEach(e=>e.href=remove_debug(e.href));
    }

    window.onerror = e =>alert(e);
}

function append_debug(u){
    const url = new URL(u);
    url.searchParams.append('__debug__', 1);
    return url.href;
}

function remove_debug(u){
    const url = new URL(u);
    url.searchParams.delete('__debug__');
    return url.href;
}
```

Whatever we put into the debug form will change the name of the window. This is important, we will touch on this later.

2 other scripts are of note: ``user.js`` and ``theme.js``. What caught my eye immediately was in ``theme.js``: 

```js
function set_dark_theme(obj) {
    const theme_url = "/static/styles/bootstrap_dark.css";
    document.querySelector('#bootstrap-link').href = theme_url;
    localStorage['theme'] = theme_url;
}

function set_light_theme(obj) {
    theme_url = "/static/styles/bootstrap.css";
    document.querySelector('#bootstrap-link').href = theme_url;
    localStorage['theme'] = theme_url;
}

function update_theme() {
    const theme = document[USERNAME].theme;
    const s = document.createElement('script');
    s.src = `/theme?cb=${theme.cb}`;
    document.head.appendChild(s);
}

document.querySelector('#bootstrap-link').href = localStorage['theme'];
```

Notice how the theme change is relient on setting the ``theme_url`` to an appropriate stylesheet. In ``user.js``:

```js

function make_user_object(obj) {

    const user = new User(obj.username, obj.img, obj.theme);
    window.load_debug?.(user);

    // make sure to not override anything
    if (!is_undefined(document[user.toString()])) {
        return false;
    }
    document.getElementById('profile-picture').src=user.img;
    window.USERNAME = user.toString();
    document[window.USERNAME] = user;
    update_theme();
}

```

the ``update_theme()`` function is called and it changes the theme to your preference, loading the theme based on what was loaded in the user object (assumed to either be `light` or `dark`). So the exploit I had in mind was a pseudo-css/HTML injection, perhaps exfiltrating data from a note we create. If we could override the theme attribute in the user object, we can do some injection - but wait, the function ``make_user_object()`` specifically accounted for object overriding. Can we bypass this? 

The [``__proto__``](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto) property is an accessor of an object's prototype - which could be some object or null. Since this object doesn't have a getter function for prototypes, this can bypass the override prevention. If we input into the debug form something like:

```json
{
   "__proto__": {},
   "theme": {
      "cb": "alert"
   }
}
```
_this gets injected into_ `window.name`. 

And refresh the page, we get exactly what we stipulated: an alert.

So we can manipulate the theme javascript callback to instead do whatever we want, and perhaps we can abuse the callback and chain together multiple ones. I would normally use ``script`` tags to achieve that, but injecting them is prohibited under the site's Content-Security Policy:

```
default-src 'none';script-src 'self' 'nonce-fe8dc57053b61e71';img-src https: http:;connect-src 'self';style-src 'self';base-uri 'none';form-action 'self';font-src https://fonts.googleapis.com
```
This stumped me for a while. We'll return to this later.

Another thing of note is that you can make notes either public or private - if you make them public, they get added into the pasteurize web server from the previous challenge, which has a known xss vulnerability ([see previous post](/posts/2020/08/googlectf-2020-pasteurize)). 

This challenge implies we need to access a private note that holds the flag, and the description of the challenge also hints that TJMike from previous is also logged into this server. So, the idea would be to use the debug capabilities and rename the ``window.name`` to perform an injection attack and grab the relevant note ID, and then redirect him to the note and somehow read the contents from there. But how?

So this is where I got stuck. Unfortunately, I didn't have enough time to figure out the challenge before GoogleCTF ended. I had the right idea - chain multiple callbacks on ``theme.cb`` to malform the window contents, and deliver a payload through the pasteurize way. Here's what I missed:

1. Chaining multiple js callbacks would require the use of the ``script`` tag, which was prohibited by the server's CSP. However, using ``<iframe>`` tags with the ``srcdoc`` property would bypass the CSP and allow for ``<script>`` tags within them. 

2. The flag was hidden in a private note in TJMike's account. I knew that exfiltration relied on redirecting TJMike to a littlethings server with the ``__debug__`` keyword added to it, so that the ``window.name`` property was set to some payload that included our server. However, it took a while for me to get a payload that worked. The key was using string concatenation. 

3. Since the note was private to TJMike, we wouldn't personally see the contents of it through our browser. It took a while for me to realize how to overcome this. Using the fact about string concatenation, we had to concat the contents of whatever the private note had to a URL request sent to our server. We would then read the note through the request URL in our server. 

## Reflections + Lessons Learned

I'm glad that I tried this challenge out, and I'm equally glad that I saw how close I actually was to the solution. Although it's bittersweet to figure out the solution shortly after the CTF ended, I'm still quite happy with myself for catching on to the exploit relatively quickly and formulating a solution that was 2/3rds of the way there. A few months ago, I would have been totally stumped on this problem. 

GoogleCTF 2020 was challenging, and I will continue to participate in harder and harder CTFs as I go. The road to self-improvement of my skills as an ethical hacker is nowhere close to conclusion, which is an exciting and slightly intimidating prospect. This was the first offering of GoogleCTF I participated in, since having started to seriously and consistently participate in CTFs in January of this year. This challenge was good practise, and I learned valuable lessons about the structure of javascript objects in HTML! I'm going to continue developing my skills, and hopefully continue to solve harder and harder challenges.

Jam