---
title: "DEF CON Quals 2021: Getting Gud + threefactooorx"
date: 2021-04-11T00:07:54-06:00
draft: false
toc: false
comments: false
categories:
  - writeups
  -
tags: 
  - writeups
  - reversing
  - web
images:
---

**AKA - How I spent some time reading custom partially-deobfuscated javascript code and actually used the Chrome debugger for once**

DEF CON - undoubtedly the most notorious, famous and recgonized CTF out there. Even people who don't do hacking know about DEF CON. The qualifiers, an event that took place this last weekend, ran for roughly 2 days and had **1** web challenge - which was more like reversing, but I'll take what I can get. 

## DEF CON, from someone who thinks she's wildly out of her depth

Anyone who is anybody in the hacker space knows about DEF CON. It is _the_ top CTF to really form a reputation out of. If you won DEF CON, you've found yourself at the top, to say the least. DEF CON has been an established security conference and CTF for years, long before I ever joined the scene. If you ever took a look at my FAQ, you'll know that being a part of DEF CON in any capacity was a lofty goal of mine. I may like CTFs, but to be on the level where I'm up against other top teams in the DEF CON leaderboard is truly a mark of recognition I would have to earn. 

I still have a long way to go, but I am glad that my team and I worked hard to land among the top 25 of DEF CON Qualifiers 2021 edition. This is big for us, given how we are still a recently new team (We only started in Feb of 2019), and we were the only Canadian team to land that good this year. Hopefully, next year, we can work harder to beat our own score! Maybe until then I'll brush up on my reversing skills so I don't do nothing for the first day :P 

Landing a respectable position among the top 25 teams in DE CON Quals is still a fact I'm absorbing and attempting to process. I know my team, and I know my team members all work hard and push against their limits to learn and grow. I'm extremely proud of each and every one of them, and certainly, I'm happy at myself for even solving a DEF CON challenge in the first place. 

I'd like to think that as I progress in the CTF world, every little victory will be celebrated and recognized. At this same time one year ago, I was thinking to myself how much I needed to do to catch up to both my teammates, and to others in the CTF world who seemed to be a on a level beyond me. I still have quite a bit of climbing to do, but allow me a minute to indulge myself and be proud of what I was able to learn in a short period of time. 

## Threefactooorx

{{< image src="/images/DEFCONQUALS2021-meme.jpeg" alt="webgang is sad" position="center" style="border-radius: 8px;" >}}

_pic from [ooo on twitter](https://twitter.com/oooverflow/status/1388764308838912000?s=20)_

The challenge, threefactooorx involved de-obfuscating some horrid js file and then satisfying the checks that it went through, to grab the flag. Specifically, you had to create an html file that checked all the boxes, and give that html file to an admin entity - who likely has the same extension - and they would send back a screenshot of what the html file looked like, loaded on their end. 

Funnily enough, this also seemed like a perfect challenge to practise chrome ndays, but despite thinking about it I didn't end up doing that - [but you could totally do it, if you wanted to](https://twitter.com/r4j0x00/status/1389064716723519489?s=20).

{{< image src="/images/DEFCONQUALS2021-chromenday.png" alt="satisfies all checks" position="center" style="border-radius: 8px;" >}}

Source was provided - it was actually a chrome extension you could load into your browser. Like mentioned earlier, the `content_script.js` was a giant, obfuscated-to-hell JavaScript file that... did stuff. 

Well, specifically, it performed some checks that, after trying it out with opening html files locally, would run on each load if it was enabled. Again, the file was obfuscated, but the very last line of the script was interesting. 

```js
    nodesadded == 
    0x1 * -0x27a + 0x3 * -0x3f8 + -0x4cd * -0x3 && _0x10b2d5[_0x39523f(-0x157, -0x1ae, -0x17e, -0x1c2)](nodesdeleted, -0x1b66 + -0x14e * 0x8 + 0x25d9) && attrcharsadded == -0x2001 + -0x2 * 0x433 + 0x49 * 0x8e && _0x10b2d5['DvvtZ'](domvalue, -0xed7 * -0x1 + -0x18f0 + 0x12a5) && (document['getElement' + _0x39523f(-0xf2, -0x127, -0x132, -0x153)](_0x5773c7(-0x141, -0x1ab, -0x16f, -0x192) + _0x5773c7(-0x131, -0x15d, -0x10d, -0xc2))['value'] = _0x336e82[_0x5773c7(-0xe7, -0xda, -0x11f, -0x111)]);
```

What are these local variables? What do they hold?

Now we turn to actually de-obfuscating the damn thing. There was a function, `OOO_0x1e05`, that we could see throughout the entirety of `content_script.js`. From the looks of it, it seemed as though `OOO_0x1e05` was a custom de-obfuscator that the challenge was using. Based on this, we could regex match and replace many of the complex bits and pieces into readable strings, to make the js somewhat more easy on the eyes. Robert Xiao did the regex matching, and the previous snippet of code above now looks like:

```js
    const _0x10b2d5 = _0x5ebd2a,
        _0xd26915 = {};
    _0xd26915['getflag'] = _0x10b2d5['xOsuT'], chrome['runtime']['sendMessag' + 'e'](_0xd26915, function(_0x336e82) {
        FLAG = _0x336e82['flag'], console.log(_0x10b2d5['KShsG'](_0x10b2d5['bppSB'], _0x336e82['flag']));
        nodesadded == 5 && _0x10b2d5['yRdwv'](nodesdeleted, 3) && attrcharsadded == 23 && _0x10b2d5.DvvtZ(domvalue, 2188) && (document['getElement' + 'ById']('thirdfacto' + 'oor')['value'] = _0x336e82['flag']);
        const _0x369bcb = document['createElem' + 'ent'](_0x10b2d5['SxgOB']);
        _0x369bcb['setAttribu' + 'te']('id', _0x10b2d5.hxkyy), document['body']['appendChil' + 'd'](_0x369bcb);
    });
```
...Which is, better, at least. I _can_ actually read it and understand what's going on, and this is what you can glean from this hellcode:

- The extension was, as mentioned earlier, performing checks on the HTML file you visit. These checks see if it was doing the following things under an HTML node with id called `3fa`:
    - added 5 nodes
    - deleted 3 nodes
    - had a total `attrcharsadded` count of 23
    - had a domvalue of 2188

- If the checks above were satisfied it would look for an element called `thirdfactooor` and give it the value of the flag.

So, in summary, add 5 nodes, delete 3, have some attribute with 23 characters in it, and have a "domvalue" of 2188. The addition and subtraction of HTML nodes is easy enough to do in Javascript, but the other 2 require some explanation: `attrcharsadded` is a local variable whose value is assigned as so:

```js
 if (_0x55a6f1.qieKm(_0x55a6f1['MRogb'], _0x55a6f1['MRogb'])) attrcharsadded += _0x8a010b['attributeName']['length'];
 ```

 Hard to fully understand what this code does, but the last part is what to focus on: the length of the `attributeName` is added to the value of `attrcharsadded`. This means we need some attribute with a name that is exactly 23 characters to satisfy this `attrcharsadded` check. 

 The domvalue is computed in the `check_dom` function, which has a lot going on:

 ```js
 function check_dom() {
    const _0x525a15 = {};
    _0x525a15.wmicU = function(_0x1a77be, _0x3e38f4) {
        return _0x1a77be(_0x3e38f4);
    }, _0x525a15['ueJYA'] = function(_0x500490, _0x3c0a68) {
        return _0x500490 != _0x3c0a68;
    }, _0x525a15['hJFjw'] = function(_0x410572, _0x33660a) {
        return _0x410572 == _0x33660a;
    }, _0x525a15.KoOZC = '#thirdfactooor', _0x525a15['RkNoD'] = 'INPUT', _0x525a15['QwGfh'] = 'QzIrw', _0x525a15['IKmUR'] = 'cunYq', _0x525a15['TCJdK'] = function(_0xee8465, _0x43b0b6) {
        return _0xee8465 + _0x43b0b6;
    };
    const _0x2c0eff = _0x525a15;
    var _0x1e6746 = document['getElementById']('3fa');
    chilen = _0x1e6746['querySelectorAll']('*')['length'], maxdepth = 0, total_attributes = _0x1e6746['attributes']['length'];
    for (let _0x28c57b of _0x1e6746['querySelectorAll']('*')) {
        d = _0x2c0eff['wmicU'](getDepth, _0x28c57b);
        if (d > maxdepth) maxdepth = d;
        if (_0x28c57b.attributes) total_attributes += _0x28c57b['attributes']['length'];
    }
    specificid = 0;
    _0x2c0eff['ueJYA'](document['querySelector']('[tost="1"]'), null) && (specificid = 1);
    token = 0;
    if (_0x2c0eff['hJFjw'](document['querySelector'](_0x2c0eff['KoOZC'])['tagName'], _0x2c0eff['RkNoD'])) {
        if (_0x2c0eff['QwGfh'] !== _0x2c0eff['IKmUR']) token = 1337;
        else {
            function _0x2351ff() {
                return;
            }
        }
    }
    return totalchars = _0x1e6746['innerHTML']['length'], _0x2c0eff['TCJdK'](_0x2c0eff['TCJdK'](_0x2c0eff['TCJdK'](_0x2c0eff['TCJdK'](chilen, maxdepth) + total_attributes, totalchars), specificid), token);
}
```

The last return sets the value of `domvalue`. It essentially sums up everything within `3fa`: `total_attributes` + `totalchars` + `specificid` + `token` + `chilen` + `maxdepth` + `innerHTML.length` + and maybe some other things, that I couldn't get from the partially de-obfuscated code. That was fine though - because `innerHTML.length` is added to this value, and we could freely control that to modify `domvalue` to the number we want. 

The final HTML file is below. It's not pretty. 

With all this information, we now needed some way to actually verify and check when we hit the necessary values for all the local variables. This is where the chrome debugger comes in.

Load the extension into your browser and open up an html file that serves as the candidate to satisfy the given checks. Placing breakpoints throughout the content script allowed me to take a look at the local variables and see what values they took: 

{{< image src="/images/DEFCONQUALS2021-allchecksgreen.png" alt="satisfies all checks" position="center" style="border-radius: 8px;" >}}

And I simply repeated this process until the vars had required values needed for the extension to be happy.

Now submit your monster html file to admin, and wait for the picture they send back :)

{{< image src="/images/DEFCONQUALS2021-flag.png" alt="satisfies all checks" position="center" style="border-radius: 8px;" >}}

HTML file below:

```html
<!DOCTYPE html>
<html>
<body>

 <div id="3fa">
 	<input id="thirdfactooor" style="width: 900px;">Poggers</input>

 	<button>button1</button>

 	<div id="nested">	
	 <div id="div2">
	  <p id="p1">You shouldnt see this.</p>
	</div>

	<div id="div3">
	  <p id="p1">You shouldnt see this.</p>
	</div>

	<div id="div4">
	  <p id="p1">You shouldnt see this.</p>
	</div>
 	</div>


</div>



<script>
//this is to get the domval to the necessary amount. It's not pretty :) 
var one = document.createTextNode("oneiuhfiluhfiulfhsjkdnaljdnakldnawkdhaeluifhefiluhwfujnsdkjnslkajfhbeiwlfhewliufhesukfhnksjdfnkje");
var two = document.createTextNode("two;sijlhkugfhhuijlok;jidlsfgkyuadiuhoijpsoIHRUGLKYEADHIOJDSPOSUDGHKSIFUJADKOSDJIHUFSJFAOKDWJISHUGUFAEIJDWFHUSGUFIAJDWEFHUSGYHUEADIJWEFHUSRFEIJADWOEFHUSRFEAJODWEFHUSGRYIUHEFAJDOWEFHUSAIJo");
var thr = document.createTextNode("thriuhygtftgyuhijutydfgukhilkjthghijhhguhijolhkgyfutyfyguhiljokihytyguhijo;ljtjgkhlj;hcguhhfgjkhljhkuashduahduhdiuhee");
var four = document.createTextNode("foukjhgfghjklkjhghjkjhghjklkjhghjklkjklkjhghjklkjklkjhghjklkjklkjhghjklkjhklkjhklkjhghjklr");
var five = document.createTextNode("pleasegivemethefleguwuasdfsssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssss");

//add 5 nodes
document.getElementById("3fa").appendChild(one);
document.getElementById("3fa").appendChild(two);
document.getElementById("3fa").appendChild(thr);
document.getElementById("3fa").appendChild(four);
document.getElementById("3fa").appendChild(five);


var d_nested = document.getElementById("nested");
//remove 3 nodes
var remove2 = document.getElementById("div2");
var remove3 = document.getElementById("div3");
var remove4 = document.getElementById("div4");
r2 = d_nested.removeChild(remove2);
r3 = d_nested.removeChild(remove3);
r4 = d_nested.removeChild(remove4);


// attributename = 23 chars
var buttatt1 = document.getElementsByTagName("BUTTON")[0].setAttribute("namenamenamenamenamenam", "pleasegivemetheflag");


</script>

</body>
</html>
```