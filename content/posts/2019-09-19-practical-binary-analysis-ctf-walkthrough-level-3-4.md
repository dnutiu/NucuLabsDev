---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2019-09-19T19:18:25+00:00'
guid: https://nuculabs.wordpress.com/?p=485
id: 485
layout: post
permalink: /2019/09/19/practical-binary-analysis-ctf-walkthrough-level-3-4/
tags:
- crackme
- CTF
- practical binary analysis
title: "Practical Binary Analysis \u2013 CTF Walkthrough \u2013 Level 3, 4"
---
Hello,


In this article I’ll present you my solution on the Chapter 5 CTF from the book [Practical Binary Analysis](https://nostarch.com/binary).


For this binary, the hint is to fix four broken things.


Running file gives us the following response:


```
binary@binary-VirtualBox:~/ctf$ file ./lvl3 
./lvl3: ERROR: ELF 64-bit LSB executable, Motorola Coldfire, version 1 (Novell Modesto) error reading (Invalid argument)
```


And the readelf command gives us:


```
binary@binary-VirtualBox:~/ctf$ readelf -h ./lvl3 
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 0b 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            Novell - Modesto
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Motorola Coldfire
  Version:                           0x1
  Entry point address:               0x4005d0
  Start of program headers:          4022250974 (bytes into file)
  Start of section headers:          4480 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28
readelf: Error: Reading 0x1f8 bytes extends past end of file for program headers
```


At this moment, it was clear that the ELF header is broken, in order to fix it I opened up [Wikipedia](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) and the [elf specification](https://refspecs.linuxfoundation.org/elf/elf.pdf).


As I went through each field manually, with Binary Ninj. As I was checking the offset of the current byte, at `0x07`, Wikipedia says: `It is often set to 0 regardless of the target platform.` I’ve changed it to `0x00`. (Note: I think this field was probably ok as it is)


At offset 0x12, the value `Specifies target instruction set architecture` and is currently invalid. From googling, I found an article titled: `Novell's Next Generation OS Will Natively Support Intel's Future IA-64 Architecture` so I set the value to `0x3E`.


[![binjahex.jpg](https://res.cloudinary.com/practicaldev/image/fetch/s--XWtd-ONO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xon0rw627fjqj7blirdt.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XWtd-ONO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xon0rw627fjqj7blirdt.png)
At offset 0x20 we have the `e_phoff` which `Points to the start of the program header table. It usually follows the file header immediately, making the offset 0x34 or 0x40 for 32- and 64-bit ELF executables, respectively`. The value `de ad be ef` is clearly invalid. I replaced the value with `40 00 00 00`.


At this moment I thought I fixed the binary and ran it, it ran and it gave me an invalid flag.


If you run the following command:


```
binary@binary-VirtualBox:/media/sf_Dropzone$ readelf -S ./lvl3 | grep .text
  [14] .text             NOBITS           0000000000400550  00000550
```


You’ll see that the .text section is marked as `0x8 - NOBITS` and it should be `0x1 - PROGBITS`. To make the change I’ve used Binary Ninja as a hex editor, opening the binary in raw mode.


From the readelf command:


```
  Start of section headers:          4480 (bytes into file)
```


The start of the section header is 4480 bytes. A section header has the length of 0x40 bytes. 4480 to hex -> 0x1180. 0x40 \* 14 + 0x1180 = 0x1500.


At offset `0x1504` we change the type from SHT\_NOBITS to SHT\_PROGBITS.


After we run the binary we get the valid flag:


```
3a5c381e40d2fffd95ba4452a0fb4a40  ./lvl3
```


- - - - - -


After finishing level 3 I wanted to go to sleep and instead I thought of running ltrace, strace on the binary and I got this:


```
binary@binary-VirtualBox:~/ctf$ ltrace ./lvl4
__libc_start_main(0x4004a0, 1, 0x7ffd6fb460e8, 0x400650 <unfinished ...>
setenv("FLAG", "656cf8aecb76113a4dece1688c61d0e7"..., 1)             = 0
+++ exited (status 0) +++
```


Didn’t expect this, very nice tho.


Thanks for reading!