---
author: "Denis Nu\u021Biu"
categories:
- Hack The Box
- Reverse Engineering
date: '2018-07-14T20:37:15+00:00'
guid: https://nuculabs.wordpress.com/?p=125
id: 125
layout: post
permalink: /2018/07/14/writeup-impossible-password-by-decoder/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- binary ninja
- challenge
- hackthebox
title: 'HackTheBox Reversing: Impossible Password'
---
Hello, this is a write-up regarding the HackTheBox reversing challenge: Impossible Password.


Please don’t look at the solution if you haven’t attempted the challenge yet, it will ruin all the fun. Try to solve the challenge by yourself first, just put some hours into it, it isn’t that hard.


I’m also a newbie at RE. Here are some hints to get you started:


- The function call convention of this program seems to be: [System V AMD64 ABI](https://en.wikipedia.org/wiki/X86_calling_conventions#System_V_AMD64_ABI).
- Debug the program in a debugger like: [edb-debugger](https://github.com/eteran/edb-debugger).


Okay, so, let’s solve this.


I will use the [Binary Ninja](https://binary.ninja/) disassembler to solve this challenge, I don’t have money for IDA. q.q (ofc I could use IDA free)


![Screen Shot 2018-07-14 at 23.12.51.png](/wp-content/uploads/2018/07/screen-shot-2018-07-14-at-23-12-51.png)


As you can see in the screenshot, there’s a `call printf` on address 0x004008ce, but before that there’s something a mov instruction with dest to `EDI`. If you double click the source address: 0x400a7f, you’ll see that it points to the character `*`. Which is printed when you first run the binary:![Screen Shot 2018-07-14 at 23.15.24.png](/wp-content/uploads/2018/07/screen-shot-2018-07-14-at-23-15-24.png)


It’s clear to me that EDI is the parameter passed to the printf, however I’m not sure why it’s passed via the EDI register, after googling for a bit, I found out the calling convention used by this binary:


> The calling convention of the [System V](https://en.wikipedia.org/wiki/UNIX_System_V "UNIX System V") AMD64[ABI](https://en.wikipedia.org/wiki/Application_binary_interface "Application binary interface") is followed on [Solaris](https://en.wikipedia.org/wiki/Solaris_(operating_system) "Solaris (operating system)"), [Linux](https://en.wikipedia.org/wiki/Linux "Linux"), [FreeBSD](https://en.wikipedia.org/wiki/FreeBSD "FreeBSD"), [macOS](https://en.wikipedia.org/wiki/MacOS "MacOS"),
[\[18\]](https://en.wikipedia.org/wiki/X86_calling_conventions#cite_note-OS_X-18)
 and is the de facto standard among Unix and Unix-like operating systems. The first six integer or pointer arguments are passed in registers RDI, RSI, RDX, RCX \[…\]


Now that we know the calling convention, it’s easier to figure out how functions are called. After the printf, a call to scanf is made.


[Scanf](https://linux.die.net/man/3/scanf) takes has the following function prototype:


```
int scanf(const char *
format
, ...);
```


Looking at the dissasembly, we can see that `EDI` holds the format string and `RSI` (which is the 64 bit version of the register ESI) holds the buffer address (I named it `scanf_input`). The next thing the program does is to print out the contents of the `scanf_input` buffer on the screen.


- *Note that the format string “%20s” means read at maximum 20 characters.*


![Screen Shot 2018-07-14 at 23.23.23.png](/wp-content/uploads/2018/07/screen-shot-2018-07-14-at-23-23-23.png)


Next, we see a call to `strcmp`.


It looks like the scanf input is compared to the key: “SuperSeKretKey”. This is the first string that we need to enter to get pass part 1.


Now, let’s look at the second part and see what we can guess from this:


![Screen Shot 2018-07-14 at 23.25.47.png](/wp-content/uploads/2018/07/screen-shot-2018-07-14-at-23-25-47.png)


1. The program prints to asteriks.
2. It reads another 20 characters into the input\_buffer.
3. It returns a modified **input\_buffer** in **generate\_second** function.
4. The returned value is compared with **input\_buffer**.
5. The function **finish\_it** is called at the end.


I didn’t want to reverse the **generate\_second** function and the **finish\_it** function, in this case it’s not even needed to do that.


You can, however get the flag by reversing the finish it function and passing the following parameter to it: **“A\]Kr=9k0=0o0;kl1?k81t”** which is found at the beginning of the main function.


An easier way and the way I did it was to patch the binary at line **0x0040095e** in order to move **RSI** to **RDI**, this way **strcmp** would be called with: `strcmp(
RSI, RSI
)` (you could also patch the jump I guess).


After we do that, the execution flow moves and **finish\_it** executes, which prints out the flag.


To reach this conclusion, I’ve had to look at the program using edb-debugger and overview the the **finish\_it** function, at first I wasn’t careful enough and I thought that **finish\_it** takes a pointer to the input buffer, but it actually takes a pointer to a string we already have. Cheers!


PS: I might update the post to include the reversed code for the finish\_it function, just for fun.


PSS: I’m realising that I shouldn’t have published this write up since this challenge is still live, sorry.