---
title: "HacktivityCon CTF: Bullseye"
date: 2020-08-09T18:48:52-06:00
draft: false
comments: false
images: ["images/ghidrabullseye.png"]
tags: ["pwn", "writeups"]
---

It's clear to see from the content of my blog that my expertise lies in web-based exploits. 
<!--more-->
However, I have mentioned before wanting to branch out and diversify my skillset in hacking; and while I definitely have more knowledge in web exploits, I also know a thing or two about pwning. Hacktivity Con CTF was a competition that ran this month, introducing several different challenges that I sunk my teeth into. I will showcase one of them here, called "Bullseye".

## Let's Begin!

Bullseye is a binary exploit challenge where we are given an executable called "bullseye". Running it through ghidra, we can locate the main function - the binary isn't stripped:

{{< image src="/images/ghidrabullseye.png" alt="Login" position="center" style="border-radius: 8px;" >}}

We're allowed a single 'write' privilege to change some aspect of the program. In pwn challenges, the goal would be to change the value in the instruction pointer - typically, common knowledge is to change the original value in the EIP register and have it take the address of the libc ``system`` function, allowing us to open a shell and do all sorts of things. We don't know where the libc location is through the global offset table, so figuring out the address of ``system`` will be our main issue. 

What's interesting is that the main() function returns by exit() - hence why we only get one write. However, if we use our one write to change exit() to instead jump back to main in the last line, we can bypass the "one write only" rule, sort of making our main() function recursive. 

Doing this, we actually get a [libc](https://www.gnu.org/software/libc/) leak, through the function ``alarm``. I find out afterwards that ASLR is disabled in this executable! This is important - with this libc leak, we can solve our "where is libc in the GOT" question, and calculate the ``system`` function's address in memory through it.
So we now have an oppurtunity to call ``system``, but what line should we replace in ``main`` to open our shell in?

Lines 16 and 20 feature a libc function called ``strtoull``. The nature of ``strtoull``, according to the standard lib documentation, is to take its single argument (provided its a string) and convert it to a long unsigned int. Since it expects my input, we can take advantage of that fact and replace it with our ``system`` calls while feeding in ``"/bin/sh"`` as our input.
We now have all the tools required to exploit this challenge.

1. Use the program's functionality to change ``exit()`` to ``main``. NOTE: I'm using python's [``pwn``](http://docs.pwntools.com/en/stable/) library.

```python
...
ch.remote("jh2i.com", 50031)

ch.recvuntil("write to?\n")
ch.sendline(exe.got["exit"])
ch.recvuntil("to write?\n")
```

2. Get the libc leak, and use it to calculate the base of libc. 

```python
alarm = ch.recvline()
alarm = int(alarm,16)
libc_base = alarm - libc.sym['alarm']
```

3. With the base of libc, calculate the address location of ``system``. Send the line of ``strtoull`` to be replaced by ``system``.

```python
ch.recvuntil("write to?\n")
ch.sendline(exe.got["strtoull"])
ch.recvuntil("to write?\n")
ch.sendline(libc_base + 0x554e0)
ch.sendline("/bin/")
```

4. Once we opened the shell, get the flag on their server!

``flag{one_write_two_write_good_write_bad_write}``

Jam