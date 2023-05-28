---
author: "Denis Nu\u021Biu"
categories:
- Hack The Box
- Reverse Engineering
date: '2018-09-22T18:37:51+00:00'
guid: https://nuculabs.wordpress.com/?p=149
id: 149
image: /wp-content/uploads/2018/07/hackthebox.png
layout: post
permalink: /2018/09/22/hackthebox-reversing-find-the-secret-flag/
tags:
- challenge
- find the secret flag
- hackthebox
- write-up
title: 'HackTheBox Reversing: Find The Secret Flag'
---
Hello,


This challenge is not that hard but it’s quite confusing. To solve this challenge very quickly all you have to do is patch it in 2 places and then run it with different arguments until the flags get’s spiten out.


Here’s a sample script that runs the binary 100 times with arguments from 1 to 100


\[code language=”bash”\]  
printf ‘start\\n’  
for i in {1..100}  
do  
 printf “$i ”  
 ./program “$i” | xxd -r -p  
 printf ‘\\n’  
done  
printf ‘\\nend\\n’  
\[/code\]


The twist of this challenge is to observe that there’s a hidden function, if you patch the execution flow to that function then you’ll see that a string from the data section is XoR’ed with a random number or argv\[1\] if it’s provided.


The binary checks if the file /tmp/secret exists, reads it and compares what it read to **0xde 0xad 0xbe 0xef**. To skip patching this jump you can use:


`echo -n -e '\xde\xad\xbe\xef' > /tmp/secret`


When you patch the call to the hidden function, make sure that you pass argv\[1\] as a parameter, that’s it. Happy hacking!


PS: The binary uses some anti-debugging techniques. I’ve observed two **ptrace** calls.


One is in the last function call from main, if you run it without a debugger the binary will spit out a flag and tells you that it’s wrong. If you run it with debugger it won’t tell you that the flag is not the right one.


The second call is made in the hidden function, it checks whether the debugger is active and if it is, it exists.