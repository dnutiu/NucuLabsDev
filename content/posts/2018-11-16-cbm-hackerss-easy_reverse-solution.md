---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2018-11-16T23:08:07+00:00'
guid: https://nuculabs.wordpress.com/?p=249
id: 249
layout: post
permalink: /2018/11/16/cbm-hackerss-easy_reverse-solution/
tags:
- cbm-hackers
- crackmes.one
title: cbm-hackers&#8217;s easy_reverse solution
---
As I continue my Reverse Engineering journey, I picked up some challenges from root-me and some from crackmes.one. This is a solution for a challenge that can be found here: [easy\_reverse](https://crackmes.one/crackme/5b8a37a433c5d45fc286ad83) it’s solution can be found at the bottom of this article, both with IDA Free and with GDB, so careful with the scrolling, it may spoil your challenge.


A picture’s worth one thousand words! If you look at the screenshot from IDA you could already guess what the flag is. ![Screen Shot 2018-11-17 at 00.36.44.png](/wp-content/uploads/2018/11/screen-shot-2018-11-17-at-00-36-44.png)


Well, let’s explain it tho. At the first, the program saves argc and argv into some local variables then it compares argc. ARGC is the argument counter of the program, by default if we call the program and pass no arguments argc will be 1, because the first argument will be the program name. ARGV will look something like this: \[“./rev50\_linux64-bit”\].


If we call the program like: ./rev50\_linux64-bit aa then argc will be 2 and argv will look like: \[“./rev50\_linux64-bit”, “aa”\]


Let’s see what the program does next. It moves \[rbp+argv\] into rax and adds 8 to it. It does that in order to skip the first argument, this way rax will point to &”aa”. Why did it add 8? Because this is a 64 bit program and *pointers in 64 bit programs have a length of 8 bytes* and 4 bytes in 32 bits programs.


Next, moves the value from rax to rax. This is what **mov rax, \[rax\]** does. rax is now “aa”. It passes rax to strlen via rdi and checks if the returned value is equal to 10. If it’s not then it prints an error message.


Now assuming we have passed a string of “123456789” and we’ve passed the strlen = 10 check. The program puts the second argument into rax and adds 4. This is equal to argv\[1\]\[4\]. rax will now be “567890”. It moves a single byte into eax and compares it with ‘@’. Since 5 != @ it errors out. That’s it, pretty simple but fun crackme.


Now, let’s see how to do this with gdb.

