---
author: "Denis Nu\u021Biu"
categories:
- Tutorial
- Linux
date: '2022-02-21T23:41:37+00:00'
guid: https://nuculabs.dev/?p=2133
id: 2133
image: /wp-content/uploads/2022/02/denis-cosmin-tnwuhygyxti-unsplash.jpg
layout: post
permalink: /2022/02/21/how-to-make-rode-audio-interface-to-work-on-linux/
tags:
- ai
- ai-1
- audio interface
- Linux
- microphone
- pulseaudio
- "R\xD8DE"
title: "How to make R\xD8DE audio interface work on Linux"
---
Hi **👋**,


I have recently updated the firmware of my RODE audio interface from 1.12.x to 1.13, and the device was no longer working properly on Linux, it had trouble with the microphone. I found out that it was an audio sampling issue.


```
dmesg -kH

[feb21 16:20] usb 1-9.3: 1:1: cannot set freq 44100 to ep 0x82
[  +0,005008] usb 1-9.3: 1:1: cannot set freq 44100 to ep 0x82
[  +0,005019] usb 1-9.3: 1:1: cannot set freq 44100 to ep 0x82
[  +0,004971] usb 1-9.3: 1:1: cannot set freq 44100 to ep 0x82
[  +0,015019] usb 1-9.3: 1:1: cannot set freq 44100 to ep 0x82
```


To fix it, I did the following:


1\. Edit Pulse’s 📖 daemon.conf.


```
nano /etc/pulse/daemon.conf
```


And add the following lines:


```
default-sample-format = s24le
default-sample-rate = 48000
alternate-sample-rate = 48000
```


2\. Disconnect the device and then kill the pulse daemon with 🔪 **pulseaudio -k**.


3\. Connect back the device 🔌, it should work without problems.


Thanks for reading and happy hacking! 🥷