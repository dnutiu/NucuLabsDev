---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2018-09-02T12:33:48+00:00'
guid: https://nuculabs.wordpress.com/?p=216
id: 216
layout: post
permalink: /2018/09/02/root-me-cracking-pe-0-protection/
tags:
- Root Me
title: 'Root Me: Cracking: PE &#8211; 0 protection'
---
Since this challenge has 0 protection we can easily solve it by searching for the string in the strings window of IDA.


Select from the menu: **View -> Open Subviews -> Strings** then double click on the:


.rdata:00404053 0000000D C Gratz man 🙂


Now you should be in the .rdata section on the binary, while the string is still selected, right click on it and click on: **Xrefs graph to…** this will give you a nice graph that you can look at.


There should be 3 functions: Main -> SUB0 -> SUB1. Find the SUB1 function in the functions window and double click on it.


Here you can see a the following data:


```
[code]
.text:00401726 interrestingFunction2 proc near         ; CODE XREF: interrestingFunction1+35↓p
.text:00401726
.text:00401726 Format          = dword ptr -28h
.text:00401726 var_C           = dword ptr -0Ch
.text:00401726 arg_0           = dword ptr  8
.text:00401726 arg_4           = dword ptr  0Ch
.text:00401726
.text:00401726                 push    ebp
.text:00401727                 mov     ebp, esp
.text:00401729                 sub     esp, 28h
.text:0040172C                 mov     [ebp+var_C], 0
.text:00401733                 cmp     [ebp+arg_4], 7 ; The password is 7 characters long.
.text:00401737                 jnz     short loc_4017AA ; the wrong password location
.text:00401739                 mov     eax, [ebp+arg_0]
.text:0040173C                 movzx   eax, byte ptr [eax]
.text:0040173F                 cmp     al, 'S' ; Here's the begining of the password.
.text:00401741                 jnz     short loc_4017AA
.text:00401743                 mov     eax, [ebp+arg_0]
.text:00401746                 add     eax, 1
.text:00401749                 movzx   eax, byte ptr [eax]
.text:0040174C                 cmp     al, 'P'
.text:0040174E                 jnz     short loc_4017AA
.text:00401750                 mov     eax, [ebp+arg_0]
.text:00401753                 add     eax, 2
.text:00401756                 movzx   eax, byte ptr [eax]
.text:00401759                 cmp     al, 'a'
.text:0040175B                 jnz     short loc_4017AA
.text:0040175D                 mov     eax, [ebp+arg_0]
.text:00401760                 add     eax, 3
.text:00401763                 movzx   eax, byte ptr [eax]
.text:00401766                 cmp     al, 'C'
.text:00401768                 jnz     short loc_4017AA
.text:0040176A                 mov     eax, [ebp+arg_0]
.text:0040176D                 add     eax, 4
.text:00401770                 movzx   eax, byte ptr [eax]
.text:00401773                 cmp     al, 'I'
.text:00401775                 jnz     short loc_4017AA
.text:00401777                 mov     eax, [ebp+arg_0]
.text:0040177A                 add     eax, 5
.text:0040177D                 movzx   eax, byte ptr [eax]
.text:00401780                 cmp     al, 'o'
.text:00401782                 jnz     short loc_4017AA
.text:00401784                 mov     eax, [ebp+arg_0]
.text:00401787                 add     eax, 6
.text:0040178A                 movzx   eax, byte ptr [eax]
.text:0040178D                 cmp     al, 'S'
.text:0040178F                 jnz     short loc_4017AA
.text:00401791                 mov     eax, offset aGratzMan ; "Gratz man :)" ; the password confirmation message
.text:00401796                 mov     [esp+28h+Format], eax ; Format
.text:00401799                 call    printf
.text:0040179E                 mov     [esp+28h+Format], 0
.text:004017A5                 call    exit
```


We can see that **arg\_0** is a pointer to a bytes array, we can assume that **arg\_4** is the length of the array. Below you can see that arg\_0 is compared to the password byte by byte. In order to convert to char from hex and back and forth, select the line and press the key R.


Thanks for reading, happy hacking!