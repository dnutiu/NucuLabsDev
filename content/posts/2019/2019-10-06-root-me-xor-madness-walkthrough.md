---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2019-10-06T17:58:08+00:00'
guid: https://nuculabs.wordpress.com/?p=490
id: 490
layout: post
permalink: /2019/10/06/root-me-xor-madness-walkthrough/
tags:
- crackme
- Root Me
title: Root Me &#8211; Xor Madness &#8211; Walkthrough
---
Hello,


In this article I will describe how I solved the **PE x86 – Xor Madness** challenge from [Root-Me](https://www.root-me.org/)


This challenge will ask you for a password and the password is also used to validate the flag. What makes this challenge interesting is that it only uses `xor`, `sub`, `call` and `ret`.


Here’s how I approached the challenge:


- Since the binary had a few function and some strings were in plain text, I tried to figure out the big picture and labeled all the function accordingly: `win`, `lose`, `check` and so on.
- I’ve figured out that there are 6 stages, each stage tries to process a part of the password. The start function is also a stage and other stages are similar.
- Next, I’ve spend a few hours debugging this challenge and documenting everything it does in order to see what it wants.


- - - - - -


To solve this challenge you need to know how the XOR operation works, please check that you know this before moving forward.


The behaviour of the challenge is simple, it computes an addr, let’s call this address SOLADDR, and it makes it point to the value 1. `[SOLADDR] = 1`.


It takes a byte of the password you’ve gave and XORs it two times. The resulting value will be an address, we can call it PASSADDR. The trick is to look at how your password is XORed and make it equal to SOLADDR.


The function that will let you proceed to the next stage only does so when the location pointed to by PASSADDR has the value 1 assigned to it. This is stored into edx. `edx = [PASSADDR]`.


The following function lets you proceed to the next stage if and only if EDX is 1:


```
weird_ret_addr_change proc near

arg2_next_stage_address= dword ptr  4

xor     eax, eax
xor     eax, [esp+arg2_next_stage_address]
sub     eax, [esp+0]    ; subtract return address from EAX
mul     dl              ; multiply eax with DL. Lower part of EDX
add     eax, [esp+0]    ; add back the return address
xor     ecx, ecx        ; reset ecx
xor     ecx, [esp+0]    ; set ecx to [esp]
xor     [esp+0], ecx    ; reset [esp]; [esp] = 0
xor     [esp+0], eax    ; set [esp] to eax
retn    4
```


We already know that EDX is set to \[PASSADDR\], which is EDX = \*PASSADDR. Each stages performs this step.


- - - - - -


In the main function, scanf scans for a maximum 63 characters (don’t trust anything you read, verify!) and stores the result in EDI, then it moved it into ESI and gets the first byte of the password\_input into EBX.


```
call    ds:scanf        
xor     esp, esp
xor     esp, edi
xor     esi, esi
xor     esi, esp
xor     edi, edi
xor     edi, esi
sub     esp, 100h
xor     ebx, ebx
xor     bl, [esi] ; ebx contains the first byte of the password
```


EBX is then xored with a value, the first 3 bytes are just for show, we only care about the resulting last byte.


```
   xor     ebx, 59307554h  ; ebx contains first byte
.text:004010C0                                         ;
.text:004010C0                                         ; Example:
.text:004010C0                                         ;
                                                       ;  in     xor     res
.text:004010C0                                         ; 0x30 ^ 0x54 -> 0x64 test case
.text:004010C0                                         ; 0x6C ^ 0x54 -> 0x38 attempt1
.text:004010C0                                         ; 0x24 ^ 0x54 -> 0x70 attempt2 - success
.text:004010C0                                         ;
.text:004010C0                                         ; The First Xor
```


The next operations loads ESP into EBX, XORs them with the *last resulting byte* from the previous operation and saves it somewhere.


```
.text:004010D0                 xor     eax, esp        ; eax = 0x0019FD74 
.text:004010D2                 xor     eax, ebx        ; eax is now xored with the our byte 0x64 (res)
.text:004010D2                                         ;
.text:004010D2                                         ; 0x0019FD74 ^ 0x64 = 0x0019FD10
.text:004010D2                                         ; the resulting address is used when setting EDX
.text:004010D2                                         ; in this case it is invalid, the correct address is
.text:004010D2                                         ; === 0x0019FD04
.text:004010D4                 xor     edx, edx 
.text:004010D6                 xor     edx, eax        ; edx = 0x0019FD10 (Addr resulting our xored byte)
.text:004010D8                 xor     ecx, ecx        ; ecx = 0
.text:004010DA                 xor     ecx, [eax]      ; try to get value from [eax]
.text:004010DA                            
.text:004010DC                 xor     [eax], ecx      ; [EAX] = 0xFFFFFFFE
.text:004010DC                                         ; eax = 0
```


Note that EDX now contains the address that we control 19FD10, which we can call PASSADDR. We obtained 19FD10 after running the program and giving it `0` as the input.


The next piece of code computes the SOLADDR by XORing ESP with two hardcoded values and makes it point to the value of 1.


```
.text:004010E0                 xor     eax, esp        ; esp = 0x0019FD74
.text:004010E2                 xor     eax, 0F2h       ; 0x0019FD74 ^ 0xF2 = 0x0019FD86
.text:004010E7                 xor     eax, 82h        ; 0x0019FD86 ^ 0x82 = 0x0019FD04
.text:004010EC                 xor     ecx, ecx        ; ecx = 0
.text:004010EE                 xor     ecx, [eax]      ; ecx = *(0x0019FD04) 
.text:004010F0                 xor     [eax], ecx      ; [eax] = [eax] ; eax = 0
.text:004010F2                 xor     byte ptr [eax], 1 [eax] = 1
```


Now, the code assigns the value pointed by PASSADDR to EDX. `EDX = [PASSADDR]`.


```
.text:004010F5                 xor     eax, eax        ; eax = 0
.text:004010F7                 xor     eax, edx        ; eax = 0x0019FD10; edx is set above; PASSADDR
.text:004010F9                 xor     edx, edx        ; edx = 0
.text:004010FB                 xor     edx, [eax]      ; edx = [0x0019FD10]; edx = [PASSADDR]
```


This is the only information you need to know to solve this challenge. You can now find the password by statically analyzing the next stages.


Thanks for reading!