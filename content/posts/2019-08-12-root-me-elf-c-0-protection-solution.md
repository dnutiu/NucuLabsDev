---
author: "Denis Nu\u021Biu"
categories:
- Uncategorized
date: '2019-08-12T17:14:03+00:00'
guid: https://nuculabs.wordpress.com/?p=462
id: 462
layout: post
permalink: /2019/08/12/root-me-elf-c-0-protection-solution/
tags:
- crackme
- elf
- Root Me
title: 'Root-Me: ELF C++ &#8211; 0 protection solution'
---
Hello!


My daily work is doing full stack web development, and reversing is a side thing that I do for fun and in my free time. In this article I will show you an easy way to solve for ELF C++ – 0 protection challenge, which can be found on [https://www.root-me.org](https://www.root-me.org/) under the cracking section.


I found this challenge a bit difficult mostly because I’m not used to reverse C++ code but, since it doesn’t contain any protection at all, all you need to do is to inspect the parameters.


![](/wp-content/uploads/2019/08/screenshot-2019-08-12-at-19.45.42.png)
If we play with the binary a little, maybe visualize it in 
, we can see that plouf is an interesting function and it’s definitely doing something, like decoding something from the binary and constructing a string.


If we fire gdb and run the program, we can setup a breakpoint right after plouf and inspect the returned value.


```
```
gdb ch25.bin
r 123
b *0x08048b51
>>> x/10x $eax
0xbffff664:	0x1c	0xc1	0x04	0x08	0x2c	0xc0	0x04	0x08
0xbffff66c:	0x14	0xc0
```
```


This doesn’t tell us much, but if you look closely you can see that it points to: **0x1c 0xc1 0x04 0x08**


Which is an address: **0x0804c11c** it is written that way because of endianness. Inspecting the value of the address in GDB will give us the output that plouf spits.


PS: Things get even more easier if you’re using GDB Peda.


#### Resources:


- 
- [https://archive.org/details/2007\_BlackHat\_Vegas-V72-Yason-Sabanal-Reversing\_C](https://archive.org/details/2007_BlackHat_Vegas-V72-Yason-Sabanal-Reversing_C)


Thanks for reading!