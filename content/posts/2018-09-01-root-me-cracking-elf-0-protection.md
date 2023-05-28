---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2018-09-01T18:42:43+00:00'
guid: https://nuculabs.wordpress.com/?p=207
id: 207
layout: post
permalink: /2018/09/01/root-me-cracking-elf-0-protection/
tags:
- Root Me
title: 'Root Me: Cracking: ELF &#8211; 0 protection &amp; ELF &#8211; x86 Basic'
---
I’ve just found out about this amazing website which offers a wide variety of challenges.


Here’s my solution for the first challenge, this one is extremely easy, you just have to pay attention to the assembly code:


![Screen Shot 2018-09-01 at 21.32.29.png](/wp-content/uploads/2018/09/screen-shot-2018-09-01-at-21-32-29.png)


Observe that the password is in plaintext and is loaded into \[ebp+s2\].


\[ebp+s1\] is passed to \[esp\] and is a pointer to the getString function, I just assumed that the function gets a string from stdin and puts it into \[ebp+s1\].


The stdin string is put into \[esp\] and the plaintext password into \[esp+4\], and are passed to \_strcmp. Next we can see that \[ebp+s1\] is compared to \[ebp+s2\]. We already know both strings. Thus the password is “123456789”.


In order to execute the binary I fired up an Ubuntu 32bit virtual machine using Valgrind and the following Valgrindfile:


\[code\]  
Vagrant.configure(“2”) do |config|  
 config.vm.box = “ubuntu/trusty32”  
end  
\[/code\]


After ssh-ing into the valgrind machine, cd into /valgrind and execute the binary. The /valgrind folder should contain the the files of the directory where the **Valgrindfile** is located.


The second challenge has a similar solution:


mov \[ebp+username\], offset aJohn ; “john”  
mov \[ebp+pass\], offset aTheRipper ; “the ripper”


Thanks for reading!