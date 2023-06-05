---
author: "Denis Nu\u021Biu"
categories:
- Embedded
date: '2020-11-30T15:59:28+00:00'
guid: http://nuculabs.dev/?p=1212
id: 1212
image: /wp-content/uploads/2020/11/image.png
layout: post
permalink: /2020/11/30/nucu-car-devlog-0x03-android/
tags:
- android application
- grpc
- nucucar
- nucuhub
- Raspberry Pi
- sensors
title: 'Nucu Car Devlog: 0x03 &#8211; Android'
---
Hello everyone!


I’ve been working lately on my little project involving Raspberry Pi and .NET, during my free time. Now, I’ve introduced a new technology, Android!


The project needed a front-end and It took me a long time to decide how to implement it and what technology to use.


I was conflicting between a cross platform desktop application or a web application. I initially thought [Electron ](https://www.electronjs.org/)would have been a great choice since it covers both, but it doesn’t work out of the box with gRPC and I needed some proxies which complicated the things on the backend, not to mention JavaScript is horrible in every imaginable way.


Luckily for me, I started dabbing in Android and somehow I manage to get a simple activity up which reads the data from the NucuCar.Sensors component.


![](/wp-content/uploads/2020/11/image-2.png?w=508)
It connects to it via gRPC directly. I didn’t expect that setting up gRPC on the Android would be so easy to be honest, you can literally just follow the gRPC tutorial for Java and everything works.


Getting that to work got me motivated and I’ve fixed some bugs in the NucuCar.Telemetry component. This made it possible for NucuCar.Sensors to run for four straight days, non-stop without crashing, it’s still running at this moment.


![](/wp-content/uploads/2020/11/image.png?w=1024)
The component reports sensor reading to Firestore every one minute, since there are 24h in a day it reports around 1.4K readings a day.


![](/wp-content/uploads/2020/11/image-1.png?w=1024)
At this point I’m pretty proud of this project, I wrote a simple Python script to plot the Temperature, Humidity and VOC resistance from my apartment, for the last 3 days:


![](/wp-content/uploads/2020/11/temperature_plot.png?w=432)
![](/wp-content/uploads/2020/11/humidity_plot.png?w=432)
![](/wp-content/uploads/2020/11/voc_plot.png?w=432)
As you can see the spikes on 2020-11-28 12:00 indicate that I’ve opened the windows and let some fresh air come into the apartment. The temperature and the humidity dropped while VOC resistance rises. The higher the VOC value the better the air quality.


This project is open-source and it can be found on my Github profile.


Thanks for reading!