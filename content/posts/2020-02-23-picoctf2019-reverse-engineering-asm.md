---
author: "Denis Nu\u021Biu"
categories:
- CTF Time
- Reverse Engineering
date: '2020-02-23T18:32:13+00:00'
guid: https://nuculabs.dev/?p=828
id: 828
image: /wp-content/uploads/2020/02/68186-picoctf-logo-og.png
layout: post
permalink: /2020/02/23/picoctf2019-reverse-engineering-asm/
tags:
- assembly
- picoCTF2019
title: picoCTF2019 Reverse Engineering Asm
---
Hello, here’s my take on the picoCTF2019 reverse engineering **asm** challenges. If you don’t know assembly, please watch a few tutorials on it:


- 
- 


## asm1 – Points: 200


CMP: Compares the first source operand with the second source operand and sets the status flags in the EFLAGS register according to the results. The comparison is performed by subtracting the second operand from the first operand and then setting the status flags in the same manner as the SUB instruction. When an immediate value is used as an operand, it is sign-extended to the length of the first operand.


JG: Jump short if greater (ZF=0 and SF=OF)


JNE: Jump short if not equal (ZF=0)


```
```
asm1:
	<+0>:	push   ebp # 
	<+1>:	mov    ebp,esp # 
	<+3>:	cmp    DWORD PTR [ebp+0x8],0x37a # 0x345 - 0x37a = -0x35


	<+10>:	jg     0x512 
 # -0x35 is greater than 0x512 because it overflowed.
	<+12>:	cmp    DWORD PTR [ebp+0x8],0x345
	<+19>:	jne    0x50a 
	<+21>:	mov    eax,DWORD PTR [ebp+0x8]
	<+24>:	add    eax,0x3
	<+27>:	jmp    0x529 
	<+29>:	mov    eax,DWORD PTR [ebp+0x8]
	<+32>:	sub    eax,0x3
	<+35>:	jmp    0x529 


	<+37>:	cmp    DWORD PTR [ebp+0x8],0x5ff # 0x345 - 0x5ff = -0x2BA
	<+44>:	jne    0x523 
 # -0x2BA is not equal to 0x523
	<+46>:	mov    eax,DWORD PTR [ebp+0x8]
	<+49>:	sub    eax,0x3
	<+52>:	jmp    0x529 
	<+54>:	mov    eax,DWORD PTR [ebp+0x8] # eax = 0x348
	<+57>:	add    eax,0x3 # eax = eax + 3
	<+60>:	pop    ebp 
	<+61>:	ret    # return eax


```
```


## Next Levels


The next levels asm2, asm3 and asm4 got a little more complex and solving them was starting to feel like a chore, my mind was hurting and I didn’t want to keep going the manual way. Lucky, I found *noahc3* and this [writeup](https://github.com/noahc3/picoctf-2019-solutions/tree/master/Reverse%20Engineering/asm2) I managed to solve them easily.


We’re going to modify the assembly and compile it with GCC, 
on my 64 bit Ubuntu I had to install the **gcc-multilib** package in order to compile 32 bit executables.


Here’s the modified code for the **asm3** task:


```
```c
.intel_syntax noprefix


.global asm3


asm3:
	push   ebp
	mov    ebp,esp
	xor    eax,eax
	mov    ah,BYTE PTR [ebp+0x8]
	shl    ax,0x10
	sub    al,BYTE PTR [ebp+0xe]
	add    ah,BYTE PTR [ebp+0xc]
	xor    ax,WORD PTR [ebp+0x10]
	nop
	pop    ebp
	ret    


```
```


What we’re going to do next is to write a simple main.c program that calls the functions with the required arguments:


```
```c
#include 


int main(void) {
    printf("Hello World!");
    // printf("Flag: %un", asm2(0xe,0x22));
    printf("Flag: %un", asm3(0xcdc485c1,0xd6bd5e88,0xe4c1548d));
    // printf("Flag: %un", asm4("picoCTF_fdb55"));
}
```
```


Next we’re going to compile the assembly code, the main.c file and the final executable:


```
```
 gcc -m32 -c asm3.S -o asm3.o -fno-stack-protector -no-pie
 gcc -m32 -c main.c -o main.o -fno-stack-protector -no-pie
 gcc -m32 -o a.out main.o asm3.o -fno-stack-protector -no-pie
```
```


If we execute **a.out** we should get our flag.


Thanks for reading!