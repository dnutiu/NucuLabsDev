---
author: "Denis Nu\u021Biu"
categories:
- Tutorial
date: '2022-12-08T22:21:30+00:00'
guid: https://nuculabs.dev/?p=2442
id: 2442
layout: post
permalink: /2022/12/08/separate-audio-and-video/
tags:
- audacity
- audio
- ffmpeg
- kdenlive
- separate
title: Separate Audio from Video (with ffmpeg)
---
Hello 👋


In this short article I will show you how to split audio from video using **ffmpeg**.


When I worked on my Udemy course I needed a way to process audio in [Audacity](https://www.audacityteam.org/) and edit the video in [Kdenlive](https://kdenlive.org/en/).


So I wrote two bash scripts, one for spliting audio and video and another one to combine the **processed audio** (usually a .wav file with the same name) with the video.


The result is the following


**split-video.sh**


```
filename=`echo "$1" | awk '{gsub(/.*[/]|[.].*/, "", $0)} 1'`
ffmpeg -i "$1" -vn -c:a copy "${filename}Temp.m4a"
ffmpeg -i "$1" -an -c:v copy "${filename}Temp.mp4"
```


**combine-video.sh**


```
filename=`echo "$1" | awk '{gsub(/.*[/]|[.].*/, "", $0)} 1'`
ffmpeg -i "./${filename}Temp.mp4" -i "./${filename}Temp.wav" -c:v copy -c:a aac "./${filename}Final.mp4"
rm "./${filename}Temp.m4a"
rm "./${filename}Temp.mp4"
```


Thanks for reading and happy hacking! 🏄