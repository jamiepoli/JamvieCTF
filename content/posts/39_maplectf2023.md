---
title: "MAPLECTF 2023: JUJUTSU KAISEN"
date: 2023-10-10T19:53:16+08:00
draft: false
toc: true
images:
tags:
  - xs-leak
  - writeups
  - esi
  - web
categories:
  - writeups 
---

MapleCTF's 2nd annual CTF was held at the same time as Hackceler8 preparation week, so for a brief couple of days in Japan I was busy helping organize 2 ctfs at once, which _I don't recommend_. 

This year, I decided to spice things up with my chals, my vie chals, by incorporating a fun prize for whichever team manages to solve all of my challenges.I had 3 challenges: JaVieScript, Blade Runner, and Jujutsu Kaisen. The first 2 I won't detail writeups as they're beginner-friendly and there already exist plenty of writeups for them. The latter one I will detail an author writeup for. 

JJK is my first soiree into some sort of full client-side web challenge. I'm really not a client-side person so I had to do some self-education to make sure I really understood these topics before I tested them out in this challenge. I would like to thank Ming/Disna for helping test the challenge and making the solve for it, and to my partner for their knowledge and wisdom that helped me make it.

This writeup assumes knowledge of client-side attacks and certain specifications of web APIs. I don't go into detail with some of the topics here outside of the direct solve, you'll need to do some extra reading if you don't have that context. 

## TL;DR
POST-based img-tag XSSI -> ESI injection -> img-creation primitive oracle -> XS-leak 

The main point of this challenge was using a graphQL regex filter to force an oracle out of pictures with ESI tags embedded in them.

## Overview
The app is a private JJK database, featuring some of the characters I like in Gege Akutami's Jujutsu Kaisen. Comments in the src and the initialization of the Dockerfile indicates that a random character in the database will have injected into their "notes" column the value of the flag. In order to get it, you will need to somehow access that random character's notes column.

Without going into detail of every file in there, I will highlight the parts of the src that make this exploit work. 


_/cache_money/default.vcl_

```conf
vcl 4.1;
import std;

backend app_backend {
    .host = "jjk_app";
    .port = "9080";   
}

backend db_backend {
    .host = "jjk_db";
    .port = "9090";   
}

acl internal {
    "localhost";
    "192.168.0.0/16";
    "172.0.0.0/8";
}

sub vcl_backend_response {
    set beresp.do_esi = true; <------- A
}

sub vcl_recv {
    if (req.http.host ~ "jjk_db" && std.ip(client.ip, "17.17.17.17") ~ internal) {
        set req.backend_hint = db_backend; <-------- B
    } else {
        set req.backend_hint = app_backend;
    }
}
```

Point A is a configuration in Varnish cache's vcl that states the cache should parse any file with a `<` present in it as XML. This includes things that _don't_ have typical XML structures, such as images or text files. This is further reinforced in the `docker-compose.yml` file:

_docker-compose.yml_
```conf
version: '3.9'

services:
  jjk_app:
    restart: on-failure
    build: ./app
    # ports:
    #   - '9080:9080'
    depends_on:
      - "jjk_db"
    environment:
      - ADMIN_NAME=placeholder
      - ADMIN_PASSWORD=placeholder
  jjk_db:
    restart: on-failure
    build: ./db
    # ports:
    #   - '9090:9090'
  varnish:
    image: varnish:stable
    container_name: varnish
    volumes:
      - "./cache_money/default.vcl:/etc/varnish/default.vcl"
        # ports:
        # - "80:80"
    tmpfs:
      - /var/lib/varnish:exec
    environment:
      - VARNISH_SIZE=2G  
    command: "-p feature=+esi_disable_xml_check" <------------- HERE
    depends_on:
      - "jjk_app"
  bot:
    build: ./bot/
    init: true
    environment:
      - CHALL_DOMAIN=https://jujutsu-kaisen-2.ctf.maplebacon.org # replaced with real domain on remote
      - ADMIN_USERNAME=placeholder
      - ADMIN_PASSWORD=placeholder
    depends_on:
      - redis
  redis:
    image: redis:6.0-alpine
  # CTF NOTE: This mimics the load balancer we have for all hosted challs. enable it on local for testing. don't attack plz
  nginx:
    build: ./nginx/
    container_name: nginx
    ports:
      - '443:443'
    depends_on:
      - "jjk_app"
```

The command `"-p feature=+esi_disable_xml_check"` enables the loose XML parsing from the container side. 

Gong back to the `default.vcl`, B is a special rule that sets any requests to a "jjk_db" to the database endpoint instead of the app's, as the vcl defines the upstream server of Varnish as the app's. This comes in handy later.


_/db/typedefs.py_
```py
import graphene
from graphene import relay
from graphene_sqlalchemy import SQLAlchemyObjectType
from graphene_sqlalchemy_filter import FilterSet

from models import CharactersModel


class CharactersFilter(FilterSet):
    class Meta: ## <------------------- C
        model = CharactersModel
        fields = {
            'name': [...],
            'cursed_technique': [...],
            'occupation': [...],
            'notes': [...],
        }

class CharactersTypeDef(SQLAlchemyObjectType):
    class Meta:
        model = CharactersModel
        interfaces = (relay.Node,)

## For mutations
class CharacterFields:
    id = graphene.Int()
    name = graphene.String()
    occupation = graphene.String()
    cursed_technique = graphene.String()
    img_file = graphene.String()
    notes = graphene.String()


class AddCharacterFields(graphene.InputObjectType, CharacterFields):
    pass
```

Point C is where a filter is defined for use in the graphQL endpoint in the application. The filterset, which is defined by a 3rd-party library called `graphene_sqlalchemy_filter`, utilizes regex-esque (it's not actually fully regex) filters that can be used on 4 different columns: `name`, `cursed_technique`, `occupation`, and `notes`. The last column is of importance as that is where the flag will exist.

_app/app.y_
```py
def upload_handler():
    if request.method == "GET":
        return render_template("newchar.html")
    elif request.method == "POST":
        name = request.form.get('name')
        occupation = request.form.get('occupation')
        cursed_technique = request.form.get('cursed_technique')
        notes = request.form.get('notes')
        upload = request.files.get('file')

        if upload is None:
            return render_template("error.html", error="You can't upload an empty file!")

        ext = upload.filename.split(".")[-1]
        
        if ext != "png":
            return render_template("error.html", error="I see what you're trying to do.")

        filename = secure_filename(os.urandom(42).hex())

        mutation = MUTATION.format(name=name, occupation=occupation, cursed_technique=cursed_technique, notes=notes, ct=cursed_technique)

        r = requests.post(GRAPHQL_ENDPOINT, json={"query": mutation, "variables": None})

        if (r.json()["data"]["addNewCharacter"]['status'] == True):
            upload.save(f"uploads/{filename}.{ext}")
            return redirect(f"/view/{filename}.{ext}")
        else:
            return render_template("error.html", error="Something went wrong when trying to upload through graphql!")
```

This is the route that handles the creation of new characters. You have the option to upload files, but they're extension-checked as png images. Notably, if you upload a character, you're then **redirected to that image.**

I won't go into the code too deep, but there is also an admin bot that logs in as me, then navigates to a user-defined URL. The cookies are not CSRF-safe. 


You now have all the components of the challenge needed to figure out the solve. Let's go ahead to see how that looks. 


## THE GRAPHQL FILTERS
The filters used in graphQL are totally fine and safe on their own. But the way that this app is coded, the filters create an opportunity to make an oracle-based attack. Since I put the flag in the notes column of one particular JJK character, a filter such as this: 

```
{
    getCharacters(
        filters: {
            notesLike: "%FLAG_SO_FAR%"
        }) {
        edges {
            node {
                name,
                notes
            }
        } 
    }
}
```

Will leak the flag char by char. 

## HIDING THINGS IN PNGS AND VARNISH XML PARSING
I've used this technique before in my previous challenge, _Makima_. The IDAT chunks of a `.png` image are critical (read: you can't make a png without them) as they define the actual bytes that comprise the pixels that make an image. IDAT chunks are the output datastream of the compression algorithm stipulated by the `.png`'s IHDR chunk, another critical metadata chunk. There can be numerous IDAT chunks, naturally, depending on the size of any given image, and behind the lens, they're just a bunch of bytes...

{{< image src="/images/IHDR_IDAT.png" alt="IHDR AND IDAT" position="center" style="border-radius: 8px;" >}}

With some readable text defining where a chunk starts and another ends. 

Now, I coded the app to ensure that only `.png` images are uploaded into the database. But remember that Varnish has been configured to parse ANY file (regardless of type) as XML if a `<` is present within - so that means we find ourselves in an image manipulation scheme.

Like I said before, the IDAT chunks of a `.png` defines the actual image. You can also, just, _hide_ bytes in those chunks and smuggle them in whilst still having a valid `.png`, as long as the critical chunks (defined in the png specification) are present and not mangled in any way. Do you get an actual image? No, probably not. But if you hide in the byte for a `<` in there, Varnish will think it's XML. 

So great, you got an opportunity to parse XML in an image but what do you do with it? 

## EDGE-SIDE INCLUDES
Before I talk about ESI tags, I'll talk about things that won't work with the primitive we have with images.

XXE's don't work, because you actually never see the output of your XXE command since you never see the character you add again. This is because the user only has capabilities to add new characters (more on that in a sec), and there's only one user, Vie (me, well, the bot) and there's no other users to log in to or register in. 

So, sure, you can parse XML but you don't see the output. Not a super useful primitive for us if we wanted to actually see stuff. So what next?

Edge-Side Includes are a cache feature that speeds up dynamic web content retrieval. It's a markup language with XML flavour. According to Wikipedia, ESIs serve the purpose of improving scaling of an application as they're meant to be blazing fast mechanisms to generate content. Varnish can utilize, parse and render ESI tags as well as many other caching solutions. 

Edge-Side Includes also have different features, such as a specific "includes" directive: inclusion of page fragments, which stipulate to the upstream server where to retrieve that content from. 

_Where_ to retrieve that content from. This smells like an SSRF attack.

Recall a little earlier point B in the overview: "B is a special rule that sets any requests to a "jjk_db" to the database endpoint instead of the app's, as the vcl defines the upstream server of Varnish as the app's". If you do a little debugging while working through the challenge, you will observe that an ESI tag with the inclusion directive to a specified website will simply make a request to that website, and put in the host header the value of that server. This plays really nicely with the custom rule observed in the `default.vcl` configuration, meaning that we can SSRF the database endpoint through an ESI tag processed by Varnish. Or, more specifically, we can SSRF the database endpoint through an ESI tag embedded in the IDAT chunks of a `.png` image that is processed by Varnish with the loose XML parsing feature explicitly enabled. 

We've gone through a lot. The exploit path is now clear: Create images programmatically such that you embed your graphql filter payload in the images you make, then upload them into the database and request for that content again to trigger Varnish and the ESI parsing. Do this in a loop, updating your filter with each correctly-guessed character in the flag. This is all well and good, but we still have a few details to iron out.

## CSRF THE BOT
The bot, Vie/me, logs in and then navigates to a URL. It can also be observed that the bot's cookies are not protected from CSRF attacks. Combining our knowledge of our exploit above, the "upload" part is not actually done by us, but done by the bot that is CSRF'ed by us. Great, sounds good. One problem though - When the bot gets the redirect to the image after uploading a new character, that image will contain the results of the filter that has our flag oracle. _We still can't see that, though._ The bot is redirected regardless of a valid/invalid image, so is there actually no oracle to be used? 

Let's talk a little bit about this oracle. So, no, you will never see the output of that image, when parsed by Varnish. Your next best thing is to go the XS-Leaks route. A redirect happens when the image is uploaded, but it can be valid/invalid. What can make a `.png` image invalid? Lots of things, but the one thing we can do easily is mangling the IDAT chunks and creating an image with a size disagreeable with the metadata. When that happens, the image refuses to load, naturally. Now, we can do that pretty easily with ESI tags. 

Recall that ESI tags are used to help scaling by rendering generated content when assembling webpages. An ESI tag with the inclusion directive will render the result of wherever that inclusion directive went into the file it's in. So, say that you have a succesfull graphql filter result, from correctly guessing a character in the flag. The length of that valid result will be longer than the length of a result of an invalid guess in the filter. You can additionally pad the length further by requesting for other fields to reflected in graphql, so you get an extra long response. This response gets rendered in the image, in place of where the ESI tag was. The extra bytes that get filled in can then mangle and warp the size of the image, making it invalid, and it therefore won't load. On the flip side, an incorrect guess yields a shorter result, so not that long of a response that is rendered where the ESI tag was in the image, and potentially not mangling the image size as much. 

That's our primitive. We can now create a valid/invalid images and detect if the load was succesful, giving us a reliable and stable XS-Leak.

## SERVICE WORKERS
We now need a way to programmatically make the admin bot upload characters with an image primitive, then detect the result of that image load. We can do this with Service Workers. 

Without going into detail, Service Workers are a modern web API that really improve the offline experience of webpages, but in my challenge they're used to intercept requests. 

We use SWs here to trigger a POST request instead of a GET request. Why?

Our payload is an image, and we are detecting if whether or not it loaded or error'ed out. Since only the admin visiting our site sees the actual image or not, we need to detect the loading using scripting to detect an `onload` or `onerror` event when an `<img src="UPLOAD ENDPOINT">` loads. But `img` tags are only ever generic GET requests, so you must use a registered SW to intercept that request and convert it into a POST to actually upload your image. When the redirect occurs, your `<img>` source will either load or not load.

## ALTOGETHER NOW

Create a script to programmatically embed ESI tags which reach the db endpoint with the graphql filter in the query parameters into the IDAT chunks of an image. Make the admin bot navigate to your site where it hosts that script and loads an `<img src="UPLOAD ENDPOINT">` where a registered SW will intercept it into a POST request and upload your ESI'd image. When the redirect occurs, observe if whether or not your `<img>` tag succesfully loaded or error'ed to determine if the flag guess you made was correct. Repeat this process until you get a flag. [Full source of solve is in here](https://github.com/ubcctf/maple-ctf-2023-public/tree/main/web/jujutsu-kaisen-2/solve). Special thanks to Ming! 

I made this challenge with the intention of it being solved in a full day, to a full day and a half. Of course, due to some issues such as the jjk_db endpoint being public-facing which was not what I wanted, a few unintended solutions existed. Such is life, but i hope that the intended solution is interesting enough to the readers here. 