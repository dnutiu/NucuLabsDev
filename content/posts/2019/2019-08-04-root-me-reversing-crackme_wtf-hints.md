---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2019-08-04T13:28:42+00:00'
guid: https://nuculabs.wordpress.com/?p=451
id: 451
layout: post
permalink: /2019/08/04/root-me-reversing-crackme_wtf-hints/
tags:
- crackme
- hints
- Root Me
title: 'Root-Me Reversing: crackme_wtf hints'
---
It’s been some time since I’ve done some Reverse Engineering and today I’ve completed a simple challenge on 
. The filename of the challenge is **crackme\_wtf** and here are some hints to get you started:


1. Determine type of FILEs.
2. Don’t be afraid to patch and use a debugger.
3. Try to eliminate the randomness. Maybe some functions that have to do with time, random numbers or the current process’s id.
4. If the last hint doesn’t yield any results… Is the required password really needed to get to the flag?


Thanks for reading and good luck!