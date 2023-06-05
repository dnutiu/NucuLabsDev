---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Reverse Engineering
date: '2018-12-25T15:48:59+00:00'
guid: https://nuculabs.wordpress.com/?p=301
id: 301
layout: post
permalink: /2018/12/25/keygenme-or-not/
tags:
- cracking
- reversing
- Root Me
title: Keygenme or Not
---
Keygenme or Not is a cute reversing challenge I found on [root-me.org](http://root-me.org). It requires an username and an activation key. I particularly enjoyed this challenge and decided to make a blog post about it. The twist of this challenge is that you can solve it without writing any code, thus the name ‘Keygenme or Not’.


If you want to solve this challenge but you’re stuck you should definitely give it one more try because solving it is very easy. Come back if you’d like to see the keygen code and my thoughts about this challenge.   
Let’s imagine that this is in fact a useful program with poorly implemented protection. The program KMN is registered to the user ‘root-me.org’ and will work only with their key. Using any other combination of username and key won’t make the program work, even if they’re valid.


Let’s see how KMN authenticates users, it gets the username and the key and after that it calls the \_auth function. After the \_auth function returns, the program checks if the authentication was successful by comparing \_auth’s return value with zero. If the return value of \_auth is any other value other than zero then the authentication fails.


 
\_\_text:0000000100000D81 call \_auth \_\_text:0000000100000D86 cmp eax, 0


We shouldn’t patch the cmp instruction and let the program do it’s “thing”, because the \_auth sets some internal state that we need later on.   
Now, I hope that you’re versed in reversing because I’ll skip some details. The only part that interests me is the loop that derives the application key from the username. I have reverse engineered this part and posted the C code in this article.


If we look at the **locLastCheck** we can see that the application key for the username ‘root-me.org’ is hardcoded and all you have to do to get the flag is provide KMN with it.


![](/wp-content/uploads/2018/12/image.png)
We can avoid the hardcoded username constraint by pathing the jz instruction to jmp, this way the program will check if the application key is correct and it will work regardless of username. And in order to build a keygen we will have to remove locLastCheck entirely and just print the computed uiSeed.


 
Below is my code for the keygen:


https://gist.github.com/dnutiu/86b4391c0ea3b9cc6a9bb3bb16ee9012 
 
And the demonstration that the patched binary actually works with valid activation keys:


```
➜  Desktop ./macho_patched
.username.
dnutiu
.activation key.
6232821
Authenticated! You can use this password to valid8
5;@PO-d{bEN~
```


 
Thank you for reading and happy holidays!