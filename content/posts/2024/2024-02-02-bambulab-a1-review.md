---
title: "My experience with BambuLab A1 and 3D printing"
author: [ "Denis Nutiu" ]
date: '2024-02-04'
categories:
  - "Review"
  - "Life"
  - "Engineering"
permalink: /2024/02/04/bambulab-a1-3d-printer-experience
image: /hugo-content/2024-02/nuculabs-dev-box.JPG
tags:
  - 3d
  - printer
  - freecad
  - cad
  - engineering
layout: post
published: true
---

![article cover image](/hugo-content/2024-02/nuculabs-dev-box.JPG)

# Introduction

Hello everyone, 👋

This is my first post of 2024, and ever since I've started the blog in ~2018, I've had
engineering ` Programming, Cloud and Engineering! 👨‍🔬;`
in the blog's description. 😄

I made a few posts related to "engineering" other than Software Engineering. In 2018, I was still studying computer
science and engineering at UPT, and I was fascinated by the Electrical Engineering courses. I figured that I'd write
blog posts about circuit analysis techniques such as Ohm's Law, Thévenin’s Theorem, Mesh Analysis, and so on.

I wrote a few of them and focused solely on Software Engineering and a few posts on [Embedded](/categories/embedded/).

---

# 3D Printing

In this post, I talk about my experience with my first 3D printer the [Bambu Lab A1](https://bambulab.com/en/a1) 
and modeling real world objects using open-source CAD software.

The printer is **intuitive** to use. I got my first print withing minutes after the initial setup and calibrations.

You don't need to perform manual calibration since the printer can self-calibrate before every print. On the first run,
it will do a more thorough calibration. Other than that, you will need to perform monthly or weekly maintenance, 
which is well documented on Bambu Lab's [website](https://wiki.bambulab.com/en/a1/maintenance).

Software support is available on all the major platforms: Windows, macOS, Linux and Android. 

You can print everything from your phone (with fewer customization options) because the printer is connected 
to your Wi-Fi network.

Since I got the printer, I successfully printed:

- Ice Scrapper — To scrap ice from my car's window. 😎
- Cutlery Holder - I threw the old one away since it was getting dirty.
- Raspberry PI case - This was the most fun print. A case that fits my Raspberry PI perfectly and just snaps in place.
- Phone Holder - For those moments when video calling my family and I need to do something else as well.

I've also used open source and proprietary software to model objects. I modeled and printed a box for my audio
interface and another box for the Raspberry PI case. I glued the boxes to some furniture to hide the objects 😅.

![ai-box.JPG](/hugo-content/2024-02/ai-box.JPG)

I glued my audio interface under my desk. The next thing I'm going to glue under my desk is the KVM switch.

![ai-box.JPG](/hugo-content/2024-02/rpi-case.jpg)

This is my Raspberry PI, which reads climate data from a BME680 sensor and displays it on my phone (using Homekit) 
and records values to a prometheus instance allowing me to view a historical record of values in a Grafana.
I wrote more about this project in the 
[BME680 Home Assistant Integration](/posts/2022/2022-01-16-bme680-home-assistant-integration/) post.
*(I did not project the case from the image myself)*.


If you plan on buying a 3D printer yourself, you can find lots of **free** models on websites such as:

- [https://www.printables.com/](https://www.printables.com/)
- [https://makerworld.com/en](https://makerworld.com/en)

To model objects I've started, using a free for personal use program called [Fusion 360](https://www.autodesk.com/products/fusion-360) and
I did not like the fact that it requires an active internet connection to use since all computations are done in the cloud.

It started lagging on my machine and I searched for alternatives that I can use while also being offline.

Then I stumbled across [FreeCAD](https://www.freecad.org/), which is open-source and free to use! 🤯

![article cover image](/hugo-content/2024-02/freecad.png)

YouTube is full of FreeCAD [tutorials](https://www.youtube.com/watch?v=8h1ofkRFdeI&list=PL7eiW2bt21YU6QEbly78kUgQCNEiDUwSH).

{{< youtube 8h1ofkRFdeI >}}

# Closing Thoughts

I did not expect to have so much fun 3D printing things for my home and friends.
Initially, I bought the printer to print D&D miniatures and I got an FDM printer because of the lack of space in 
my apartment.

The printer got useful really quickly when I printed a box for hiding my audio interface under my desk and some 
cable clippers, helping me to effectively reduce cable clutter.

BambuLab A1 is an easy and straightforward to use printer. The price of the printer is fair, and if you're looking for
more advanced printers, you can find them on their [website](https://bambulab.com/en/compare). I like that everything
just works straight out of the box, the printer has Wi-Fi, a camera for monitoring, notifications for my mobile phone when
something goes wrong, easy pause and resume for prints, auto calibration, easy setup and maintenance.

The open-source community also offers us a great and capable tool [FreeCAD](https://www.freecad.org/) 
that we can use for free to bring our designs to life.

Thank you for reading! 📖