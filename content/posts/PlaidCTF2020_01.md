---
title: "PlaidCTF 2020: Contrived Web Problem"
date: 2020-04-26T00:48:12-06:00
draft: true
---


This was a CTF I unfortunately didn't have the time for, as I was busy doing finals in April :(. My team let me know about this cool and unqiue problem, and I'm glad they did! 
<!--more-->


This was a journey in understanding internet protocols that deepened my knowledge of them to completely new levels, so I'm really grateful I got the chance to try this problem out - even though I tried it after the CTF ended :P



Let's Begin!
----

This problem really holds up to its name. The website is simple - you have the option to login or register as a new user. Uniquely, we are given the source code for the problem. 

First things first then, since we have the source code, let's see if we can find any flag.txt files in it! 


That was really easy to find...
It's in the services dockerfile, which is only used by api, email and server services. So, the vulnerability probably is within one of these services. 

I checked out api first - and I see that the only protocols that it allows are http, https and ftp. 

Looking at email next, which uses nodemailer. I'm not too familiar with it, so I check out its documentation: 



This seems like a good loophole to exploit - we just need to specify the filepath and nodemailer will send an email with an attachment of whatever file we specified by the filepath. So, we will literally ask it to email us flag.txt! 

Now the hard part - how do we send a request to the server to send us the email with the flag as an attachment? 

I spent a long time trying to figure out how to send a rogue request to the rabbitmq server that will send us the email we want. I spent hours just browsing through the source code, not really paying attention... 

Until I came across this piece of code in the api index.ts file: 

```typescript
    app.get("/image", async (req, res) => {
        let { url } = req.query;
        if (typeof url !== "string") {
            return res.status(500).send("Bad body");
        }

        let parsed = new URL(url);

        let image: Buffer;
        if (parsed.protocol === "http:" || parsed.protocol === "https:") {
            const imageReq = await fetch(parsed.toString(), { method: "GET" });
            image = await (imageReq as any).buffer();
        } else if (parsed.protocol === "ftp:") {
            let username = decodeURIComponent(parsed.username);
            let password = decodeURIComponent(parsed.password);
            let filename = decodeURIComponent(parsed.pathname);
            let ftpClient = await connectFtp({
                host: parsed.hostname,
                port: parsed.port !== "" ? parseInt(parsed.port) : undefined,
                user: username !== "" ? username : undefined,
                password: password !== "" ? password : undefined,
            });
            image = await ftpClient.get(filename);
        } else {
            return res.status(500).send("Bad image url");
        }
                if (!isPNG(image)) {
            return res.status(500).send("Bad image (not a png)");
        }

        res.type(".png").status(200).send(image);
    })

    app.listen("4101");

```
Image: the profile picture we would upload when we register as a new user. For HTTP and HTTPS protocols it's a simple GET method, not much to do there. But for ftp? 
It asks for the username, password and filename, _unchecked_, then asynchronously asks the server for the image that matches the filename of the image. 
FTP is a text-based protocol, and since username and password fields are passed to it unchecked, we could definitely inject some protocol commands and make ftp do things the user didn't intend for us to do.

Here's something I learned in my internet computing class about the FTP protocol: the client must send the server the name/address and port that they want the server to connect to and send files to. Know where this is going? 

We are going to hide FTP commands in an HTTP message that we hide in our profile picture png, and through some working around, will let the FTP server open a connection with the rabbitmq server that will allow us to send the request to send an email with the flag.txt as an attachment to it. Confused? Same! 

But nonetheless I had a vague idea of what I wanted to do so I began working on my exploit.