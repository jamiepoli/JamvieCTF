---
title: "UTCTF 2020: Epic Admin Pwn  "
date: 2020-04-22T00:46:19-06:00
draft: true
author: "Jamvie"
---

I particpated in UTCTF with my team in March 2020. A very fun SQLi-based attack! 

We are presented with a clean and minimal login page. The challenge's description says that "the password is the flag". Well, since this is only a login page, I'd figure to try and get into admin somehow.

Initial attempts to do some scoping for SQL vulnerabilities didn't do anything. Inputting a single quote ' mark wouldn't show anything useful. So, I went in kinda blind, and did a pretty standard SQL attack: ```admin--``` which, if there was vulnerable SQL queries to be had, would malform the query to only return the entries where the username == admin. 
And it worked! 


It's a static welcome page, so I didn't get anything useful past learning that the admin's username was, in fact, admin. And the challenge's description told me as such - if I wanted the password, I needed to do something else. 

The fact that I got into admin meant that I malformed the SQL query so that it would ignore any sort of password checking. But, I kinda need some form of password validation in order to get any headstart on what it was. Luckily, SQL utilizes alot of logic-based keywords that lets us essentially guess the password with custom SQL we inject into it. We would want to inject rogue SQL so that the underlying query would look like,

```SQL

admin' AND password LIKE .....

```

While I use the SQL 'LIKE' keyword here, another keyword called "substr" exists that I prefer. A teammate of mine actually created a script that bruteforced the alphanumerics letter by letter until we printed the password, but I decided to take a shot at creating one myself using binary search ~~because I haven't properly and correctly implemented that algorithm since 2nd year~~:

```python
```

The idea here is to test out every character against the password's character at the given index. If they match, store it into our buffer array. When we reach the nullbyte character which terminates strings, then we print out the buffer, which should have correctly found the password! 

