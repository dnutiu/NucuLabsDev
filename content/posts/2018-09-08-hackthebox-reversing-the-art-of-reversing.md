---
author: "Denis Nu\u021Biu"
categories:
- Hack The Box
- Reverse Engineering
date: '2018-09-08T14:00:16+00:00'
guid: https://nuculabs.wordpress.com/?p=172
id: 172
layout: post
permalink: /2018/09/08/hackthebox-reversing-the-art-of-reversing/
title: 'HackTheBox: Reversing: The Art Of Reversing'
---
The Art of Reversing challenge was a fun and quick challenge! I did get the flag but I did not write a keygen for it, I didn’t even need to look under the hood in order to get the flag. The scheme is very simple actually.


The days are represented by roman numbers. The letters are not the original and the number is read from right to left.


Fuzzing with the input you can deduce the following values:


d = 100  
m = 50  
y = 10  
wj = 4  
w = 5  
j = 1


The value of 365 in this weird format would be: **wymddd =**100\*3+50+10+5


If we try random inputs in order to solve for the username, we can see that the output is just permuted in some way


c a t h h t k e e p a l n  
Input: 1 2 3 4 5 6 7 8 9 A B C D  
Output: 3 2 5 6 1 D 4 C 7 8 A 9 B


And the flag: hacktheplanet365