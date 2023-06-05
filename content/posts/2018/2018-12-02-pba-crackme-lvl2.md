---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2018-12-02T13:01:54+00:00'
guid: https://nuculabs.wordpress.com/?p=269
id: 269
layout: post
permalink: /2018/12/02/pba-crackme-lvl2/
tags:
- crackme
- level2
- practical binary analysis
title: Practical Binary Analysis &#8211; Crackme Walkthrough &#8211; Level 2
---
I’ve recently finished reading a book called Practical Binary Analysis which I consider a state of the art book (review will come soon) and I would like to post my solution to the crackme found in chapter 5.


> 1\. A New CTF ChallengeComplete the new CTF challenge unlocked by the oracle program!You can complete the entire challenge using only the tools discussedin this chapter and what you learned in Chapter 2. After completingthe challenge, don’t forget to give the flag you found to the oracle tounlock the next challenge


To do the crackme and view the code covered in the book you don’t have buy it, you can download it for free from the official website. If you’re not on a Linux host system, I recommend downloading the VM which comes with the book from here: 


In chapter 5 the author analyzes the first level of the crackme using only Linux tools and explaining every step in detail, I will move past the first level and go straight to level 2.


Let’s get started, download the code or the VM and navigate to the chapter5 directory if you don’t see the **lvl2** binary you can unlock it by executing the following command in the terminal:


```
./oracle 84b34c124b2ba5ca224af8e33b077e9e
```


Let’s let’s see what’s this binary is all about, we can identify that it’s an elf binary with the file command. Knowing that it’s an elf, can can do a **readelf -a lvl2 > lvl2\_readelf.txt** on it, which gives us the following output:


```
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x400540 <- this is what we need.
  Start of program headers:          64 (bytes into file)
  Start of section headers:          4624 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28
  ...
```


If we run the binary we just get a random byte on the screen, let’s open gdb and set a breakpoint to the entry point address by typing the following commands:


```
gdb lvl2
b * 0x400540 
r # To run the program. It will now stop at the breakpoint we set.
set disassembly-flavor intel # To show the disassembly in intel syntax.
disassemble # Under normal circumstances this would show us the disassembly
# In this case it doesn't work, we can view the instruction on which we are 
# by using x/i $pc. $pc is the program counter register.
x/i $pc
# => 0x400540:	xor    ebp,ebp
display/i $pc # This makes GDB automatically display the next instruction to be executed, each time your program stops.
si step instruction
si
si
...
q # This doesn't seem to work, let's quit and see what else can we do.
```


If we do a **ltrace ./lvl2** we can see the library calls the program does:


```
__libc_start_main(0x400500, 1, 0x7fff5c6b0ec8, 0x400640 <unfinished ...>
time(0)                                                      = 1543667140
srand(0x5c027dc4, 0x7fff5c6b0ec8, 0x7fff5c6b0ed8, 0)         = 0
rand(0x7ff89538f620, 0x7fff5c6b0dac, 0x7ff89538f0a4, 0x7ff89538f11c) = 0x6e66a24a
puts("d6"d6)                                                   = 3
+++ exited (status 0) +++
```


If we google for **\_\_libc\_start\_main(**) we’ll find that:


```
int __libc_start_main(int *(main) (int, char * *, char * *), int argc, char * * ubp_av, void (*init) (void), void (*fini) (void), void (*rtld_fini) (void), void (* stack_end));
```


> The \_\_libc\_start\_main() function shall initialize the process, call the *main* function with appropriate arguments, and handle the return from main().
> 
> 
http://refspecs.linuxbase.org/LSB\_3.0.0/LSB-PDA/LSB-PDA/baselib—libc-start-main-.html  
> 


Which means the first address is the pointer to the main function. From our ltrace call we have: **0x400500**


If we disassemble the binary with lvl2 with: **objdump -d -M intel lvl2 > lvl2\_dasm.txt** we can see that the address we’re hunting is at the beginning of the .text section.


```
 0000000000400500 <.text>:
  400500:	48 83 ec 08          	sub    rsp,0x8
  400504:	31 ff                	xor    edi,edi
  400506:	e8 c5 ff ff ff       	call   4004d0 <time@plt>
  40050b:	89 c7                	mov    edi,eax
  40050d:	e8 ae ff ff ff       	call   4004c0 <srand@plt>
  400512:	e8 c9 ff ff ff       	call   4004e0 <rand@plt>
  400517:	99                   	cdq    
  400518:	c1 ea 1c             	shr    edx,0x1c
  40051b:	01 d0                	add    eax,edx
  40051d:	83 e0 0f             	and    eax,0xf
  400520:	29 d0                	sub    eax,edx
  400522:	48 98                	cdqe   
  400524:	48 8b 3c c5 60 10 60 	mov    rdi,QWORD PTR [rax*8+0x601060]
  40052b:	00 
  40052c:	e8 6f ff ff ff       	call   4004a0 <puts@plt>
  400531:	31 c0                	xor    eax,eax
  400533:	48 83 c4 08          	add    rsp,0x8
  400537:	c3                   	ret    
  400538:	0f 1f 84 00 00 00 00 	nop    DWORD PTR [rax+rax*1+0x0]
  40053f:	00 
```


What does the binary do? It does a srand(time(0)) and calls rand(), does some computation and prints something from **QWORD PTR \[rax\*8+0x601060\]** if we go back to the debugger or look at the readelf output again we can identify that **0x601060** is not far from the .data section which starts at **0x601040**. If we dump the section we get the following:


```
binary@binary-VirtualBox:~/code/chapter5$ objdump -s --section .data lvl2

lvl2:     file format elf64-x86-64

Contents of section .data:
 601040 00000000 00000000 00000000 00000000  ................
 601050 00000000 00000000 00000000 00000000  ................
# This is were we should be looking
 601060 c4064000 00000000 c7064000 00000000  ..@.......@.....
# The addresses are in hexadecimals thus if we add 16 bytes (0x10 in hex) to 601060 we get 601070, a byte is represented as two adjacent hexadecimals like 0xCA
 601070 ca064000 00000000 cd064000 00000000  ..@.......@.....
 601080 d0064000 00000000 d3064000 00000000  ..@.......@.....
 601090 d6064000 00000000 d9064000 00000000  ..@.......@.....
 6010a0 dc064000 00000000 df064000 00000000  ..@.......@.....
 6010b0 e2064000 00000000 e5064000 00000000  ..@.......@.....
 6010c0 e8064000 00000000 eb064000 00000000  ..@.......@.....
 6010d0 ee064000 00000000 f1064000 00000000  ..@.......@.....
```


This section looks like it’s containing pointers to something followed by a null: **ca064000 00000000**. The address **ca064000** is in little endian, to make it in big endian we rewrite it as: **004006ca** which is an address that is closed to the .rodata section. Let’s dump it:


\[16\] .rodata PROGBITS **00000000004006c0**  000006c0 0000000000000034 0000000000000000 A 0 0 4


```
binary@binary-VirtualBox:~/code/chapter5$ objdump -s --section .rodata lvl2

lvl2:     file format elf64-x86-64

Contents of section .rodata:
 4006c0 01000200 30330034 66006334 00663600  ....03.4f.c4.f6.
 4006d0 61350033 36006632 00626600 37340066  a5.36.f2.bf.74.f
 4006e0 38006436 00643300 38310036 63006466  8.d6.d3.81.6c.df
 4006f0 00383800                             .88.
```


And this is most likely the flag we’re searching for. Thanks for reading 😀