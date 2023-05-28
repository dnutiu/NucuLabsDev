---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2019-10-27T14:31:54+00:00'
guid: https://nuculabs.wordpress.com/?p=536
id: 536
layout: post
permalink: /2019/10/27/root-me-gb-basic-gameboy-crackme-walkthrough/
tags:
- crackme
- gameboy
- Root Me
- z80
title: 'Root-Me: GB &#8211; Basic GameBoy crackme walkthrough'
---
Hello,


In this article I will describe how I solved the [GB – Basic GameBoy crackme challenge](https://www.root-me.org/en/Challenges/Cracking/GB-Basic-GameBoy-crackme) from Root-Me.


Before reading this article you should attempt to solve the challenge on your own. Start by reading/skimming through the GameBoy CPU manual then download an emulator such as [mGba](https://mgba.io/) and play with the ROM. To disassemble the ROM I’ve used [Ghidra](https://github.com/NationalSecurityAgency/ghidra) and [mgbdis](https://github.com/mattcurrie/mgbdis).


- - - - - -


After reading through the GameBoy CPU manual, I’ve opened the file in Ghidra and found these interesting strings:


![](/wp-content/uploads/2019/10/1.png?w=611)
If you emulate the ROM using mGba and press the Left key then the Enter key, the message “Left” will appear on the screen, for the moment I can assume that the strings are related to the message.


Ghidra doesn’t know about the GameBoy ROM structure and I don’t know either, so I’ve tried to disassemble the ROM with the help of a specialized GameBoy disassembler:


```
➜  mgbdis git:(master) ✗ ./mgbdis.py crack_rom.gb 
Loading "crack_rom.gb"...
ROM MD5 hash: 51ff0d38b93107c38a753d9b0ee1576c
Generating labels...
Generating disassembly..
Disassembly generated in "/home/denis/Downloads/mgbdis/disassembly"
```


By opening the output of the disassembler and searching for `0459` (the address of the Nope string) I’ve found code that references it at `0415`.


![](/wp-content/uploads/2019/10/2.png?w=585)
I went back to Ghidra and disassembled the block at `0415` by pressing the D key.


![](/wp-content/uploads/2019/10/3.png?w=911)
This seems to be the block that checks some values from RAM and prints the flag if the values are correct.


The first comparison is done for the `Left` key. It loads the value from the RAM address 0xc0b0 into the A register.


The A register is then compared to `0x32` and there’s a conditional jump to the NopeBlock. `JP NZ,NN ;jumps if Z is reset`


```
                             LAB_ram_03c0                   
        ram:03c0 11 b0 c0        LD         DE,0xc0b0
        ram:03c3 1a              LD         A,(DE=>DAT_ram_c0b0)
        ram:03c4 4f              LD         C,A
        ram:03c5 fe 32           CP         0x32
        ram:03c7 c2 15 04        JP         NZ,NopeBlock
        ram:03ca 18 03           JR         LAB_ram_03cf
        ram:03cc c3 15 04        JP         NopeBlock
```


`CP is a subtraction from A that doesn't update A, only the flags it would have set/reset if it really was subtracted. If A == N, then Z flag is set.`


So if A = 0x32 then Z is set and the jump to NopeBlock only happens if Z is reset. Sounds easy to me. To pass the check the value at `0xc0b0` needs to be 0x32.


If we look at the memory view in the mGba emulator, the location at `0xc0b0` has the value `0x39`. If we press the `Left` key the value is decremented.


![](/wp-content/uploads/2019/10/gbafinal-export.png?w=750)
To get the flag you must solve for the Up, Down and Right keys.


Thanks for reading! <3