---
author: "Denis Nu\u021Biu"
categories:
- Windows
date: '2021-10-08T19:51:41+00:00'
guid: https://nuculabs.dev/?p=1698
id: 1698
layout: post
permalink: /2021/10/08/%f0%9f%92%bbremote-desktop-logon-attempt-failed-%e2%9b%94/
tags:
- logon attempt failed
- remote desktop
- windows 10
title: "\U0001F4BBRemote Desktop: logon attempt failed \u26D4"
---
Hi 👋


Have you ever tried to setup your Windows’s Remote Desktop functionality and failed with the following error message “**logon attempt failed**“, knowing that your credentials were correct and that you’re on the same Wi-Fi network with the computer you’re trying to access?


Well, I did.


Most of the solutions you’ll find on other blogs make you edit your Window’s registry, network settings and so on, which in my case was not needed, and it is somehow overkill.


![](/wp-content/uploads/2021/10/image-1.png?w=385)
Logging in **with a password** and my Microsoft account on the computer I wanted to setup Remote Desktop solved the issue.


What I had to do was to go in **Settings > Accounts > Sign-in Options**, disable Windows Hello PIN and all other sign in options, setup **Password sign-in option** and then relog on my PC.


![](/wp-content/uploads/2021/10/image.png?w=1024)
That’s it.


## Windows 11


If you’re on Windows 11 then you don’t have sign in with a password as an option. What worked for me is to reset my PIN using my Microsoft account during the login screen.


After resetting my PIN the Remote Desktop started working.