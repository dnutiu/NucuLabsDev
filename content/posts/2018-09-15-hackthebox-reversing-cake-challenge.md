---
author: "Denis Nu\u021Biu"
categories:
- Hack The Box
- Reverse Engineering
date: '2018-09-15T06:37:44+00:00'
guid: https://nuculabs.wordpress.com/?p=141
id: 141
image: /wp-content/uploads/2018/07/hackthebox.png
layout: post
permalink: /2018/09/15/hackthebox-reversing-cake-challenge/
tags:
- cake
- challenge
- hackthebox
- reversing
- write-up
title: 'HackTheBox Reversing: Cake Challenge'
---
Hello,


If you open this binary in Binary Ninja or any other disassembler, you’ll see a that the flow is quite weird. After looking at the strings tab I found **UPX** listed in there, this explains the weird graph.


> UPX is an open-source packer. A packer is just a piece of software that compresses the binary, when the binary is run it will unpack itself. Packing an exe could reduce it’s size by 30% or so.. since UPX is free you could also use it to unpack binaries. Unpacking is left as a exercise to the reader (I’m lazy)


After you’ve unpacked the binary, running it will no longer works but that’s okay. I continued my analysis with 2 copies of the binary, the unpacked one for static analysis and the packed one for debugging.


After that I found out that you can make the binary work after unpacking if you disable ASLR. That’s a DLLCharacteristic, you may google how to do that.


After looking at the main function, I found a call to a function which starts with a lot of printf. This is our **introduction** function.


![Screen Shot 2018-07-24 at 21.07.49.png](/wp-content/uploads/2018/07/screen-shot-2018-07-24-at-21-07-49.png)


Here we can observe that a 10 digit password is required, but the scanf read count is compared with 15. One block below a 15 digit password is required, we can skip this and enter the 15 digit password directly.


The rest is just hunting for the password, basically, the password is compared at various places with hardcoded characters. If your hunt is successfully then you’ll find the password, use a debugger if you struggle, the only ‘hard’ thing is that the password is not compared in order.


For example: After you enter the 15 digit password, the input is strncpy’ed into a buffer and a function is called. You can check on [MSDN](https://msdn.microsoft.com/en-us/library/5dae5d43.aspx) the parameters that strncpy\_s takes.


![Screen Shot 2018-07-24 at 21.19.07.png](/wp-content/uploads/2018/07/screen-shot-2018-07-24-at-21-19-07.png)


Next, the function that I’ve called **digits\_check** is called. Let’s see what this function does.


![Screen Shot 2018-07-24 at 21.21.11.png](/wp-content/uploads/2018/07/screen-shot-2018-07-24-at-21-21-11.png)


Now everything resumes at discovering the password bits by bits. If you hunt is successful then you’ll have something like this: **h@ckth3parad1$E**


Thank you for reading and happy hacking!