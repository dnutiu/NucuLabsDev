---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2018-06-30T07:44:17+00:00'
guid: https://nuculabs.wordpress.com/?p=84
id: 84
layout: post
permalink: /2018/06/30/lena151s-reversing-tutorials-1-5/
tags:
- cracking
- lena151
title: Write-up on Lena151&#8217;s Reversing Tutorials [1-5]
---
I decided that I want to do something new and I started to get into assembly and reverse engineering, hopefully I can build a career with this.


I watched some plural sight videos on the RE that gave me a nice introduction on subject but sadly nothing practical, and the content was not very detailed.


I’ve tried to read a book on RE, Practical Reverse Engineering but that didn’t help, I didn’t understand assembly that well and I ended giving it up… until the beginning of this month, when I took initiative and started doing to Intro to x86 course from open security training, quite a nice course as it helped me understand assembly on a basic level, a thing that gave me courage to pursue RE further. I did the binary bomb challenge from the course but It gave me no satisfaction at all, I couldn’t get the binary to run in the debugger and I had to use the IDA disassembler in order to solve it, then check with google if my solutions were correct. That was fine until phase 6, where I gave up as the assembly was difficult to understand. 😞


Nonetheless, I moved forward and subbed upon Lena151’s tutorials, what a gem they were! A collection of SWF videos from 2006, those are 12 years old! I don’t know how modern RE is taught but Lena151’s tutorials teach you in a way that’s more likely a cracker’s path, for, pirating software.


Anyways, I wrote down below my experience with the challenges, every one is meant to teach you something new.


1\. **Olly + assembler + patching a basic reverseme**  
In this scenario, you are given a executable that pops up the message “License expired, please purchase” message. You must find a way to bypass this.


This challenge is easy, is has a nice introductory swf video that walks you on solving it and a document that explains assembly language so and so.


What you will learn after completing this challenge is how to use the Olly debugger and how to patch the binary. By patching the binary you will bypass the license check and jump directly to the “goodboy”. This is definitely a good start in RE since you get some features of the debugger explained straight away, I’ve used x64dbg while solving this.


2\. **Keyfiling the reverseme + assembler**


This one is the same as the first, except that here you will learn how to bypass the license requirements by reading the assembly and understand it so that you can craft a custom license. If you study carefully the ASM you’ll see that this challenge is easy as well.


3\. **Basic nag removal + header problems**


This challenge is more interesting than the previous ones. You’ll learn about the PE header of binaries.


> The sections in the PE header is what is really needed to execute a program. The header stuff is there to help win32 loader to find it. Section names can be anything. The entry point of the program is located at the last 4 bytes of the PE header, the entry point is stored in little endian mode.


After working my way through the exe with x64dbg I found out that I can’t easily patch the EntryPoint… I tried however to patch it via CFF’s explorer hex editor and that was successful, a better solution is to use PE Editor because everything you have to do is edit an input field.


4\. **Basic + aesthetic patching**


This is where things get interesting, you get to reverse a real application. In this tutorial you’ll learn about structured exceptions handlers and how olly can’t handle them properly, not only that but as you progress through the video you’ll learn how to patch the application an runtime in order to remove several trial restrictions. For a beginner like me this was pretty amazing to watch!


I had some errors while installing the provided program but the program worked.


An extremely fine looking piece of software! (developed in the 2000’s.) The software is limited, as it allows you to introduce only 4 contacts.


Sometimes programs may raise exceptions and you may want that your debugger ignores them. The allow a range of exceptions feature in x64dbg was in Settings>Preferences, which was a bit weird. imo, it should have been in the debugger settings.


At this moment, I stopped the video right before the add entry RE and I started to work on this by myself. I took me some time before I figured out how to work with x64dbg but in the end I’ve made it! The tick was to use “Run till return” step over to the caller, set a breakpoint and “Run”, this way, when the Add entry button was pressed the breakpoint was hit and you could see exactly which code triggered the please register message.


Fun fun fun. Resuming the video now..


A common rule: use strings just for confirmation not for finding stuff. The next step was to replace the unregistered strings, I had some trouble doing that with x64dbg and dropped it.


5\. **Comparing on changes in cond jumps, animate over/in, breakpoints**


In this challenge you’ll reverse another real world application and you’ll learn about software, hardware and memory break points. You’ll also learn about the call stack and how to use the animate over/trace functionalities of the debugger.


This challenge was fun to do, it helped me to familiarise myself with the debugger (for example the call stack doesn’t refresh on pause, you’ll have to click on EIP or something to refresh) and it helped me understand that I have nothing to do in dll’s, at least in this case. When I see a function that’s called in the dll I just step over it.


Thank you for reading! I will continue my reverse engineering journey with Lena’s tutorials and after that I’m not sure what I will do. I’m planning on purchasing some books on it, hopefully they will help.


### Resources:


- http://opensecuritytraining.info/
- https://tuts4you.com/e107\_plugins/download/download.php?list.17