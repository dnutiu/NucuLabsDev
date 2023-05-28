---
author: "Denis Nu\u021Biu"
categories:
- Tutorial
date: '2022-12-11T12:37:20+00:00'
guid: https://nuculabs.dev/?p=2469
id: 2469
layout: post
permalink: /2022/12/11/how-to-fix-davinci-resolve-audio-issues-on-ubuntu-20-10/
tags:
- audio fix
- audio not working
- davinci resolve
- fix
- Linux
title: How to fix DaVinci Resolve audio issues on Ubuntu 20.10
---
Hello 👋,


To fix audio issues on a fresh install of DaVinci Resolve 18 on my Ubuntu 20.10 PC I did the following things:


## 1. List all the available sound cards


```
cat /proc/asound/cards
 0 [NVidia         ]: HDA-Intel - HDA NVidia
                      HDA NVidia at 0xfc080000 irq 94
 1 [AI1            ]: USB-Audio - RODE AI-1
                      RODE Microphones RODE AI-1 at usb-0000:2d:00.3-1.3, full speed
 2 [Generic        ]: HDA-Intel - HD-Audio Generic
                      HD-Audio Generic at 0xfc400000 irq 96
 3 [U0x46d0x81b    ]: USB-Audio - USB Device 0x46d:0x81b
                      USB Device 0x46d:0x81b at usb-0000:2d:00.3-1.4, high speed
```


List all the available sound cards a chose a default. I like playing audio though my headphones when editing and the headphones are connected to my **USB Audio Interface RODE AI-1**, this is what I want to use as the default card.


## 2. Set the default card for Alsa


To set the default card I’ve created a new file **/etc/asound.conf** and pasted the following contents into it:


```
   defaults.pcm.card 1
   defaults.ctl.card 1
```


The number 1 represents the number of the default sound card, in my case it is:


```
 1 [AI1            ]: USB-Audio - RODE AI-1
                      RODE Microphones RODE AI-1 at usb-0000:2d:00.3-1.3, full speed
```


After it’s set, reload alsa with: **sudo alsa force-reload**


## 3. Start DaVinci Resolve 18 and restart pipewire


Each time you start DaVinci Resolve 18 you may need to run the following command in order to get audio working:


**systemctl restart –user pipewire**


*Note: DaVinci Resolve can’t play AAC audio files on Linux.*


*Note: Your audio may stop working outside DaVinci resolve while editing and after closing it, to fix it run the above command again.*


## 4. If this breaks your audio on other software


Remove **/etc/asound.conf** file and restart Alsa & Pipewire. We need to wait for an official fix. 🙁


- - - - - -


Thanks for reading! I hope you’ll find this useful.


Happy hacking! 🦾