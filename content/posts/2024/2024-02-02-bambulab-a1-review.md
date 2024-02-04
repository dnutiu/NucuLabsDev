---
title: "Bambu Lab A1 3D Printer Review"
author: [ "Denis Nutiu" ]
date: '2024-02-02'
categories:
  - "Review"
  - "Life"
  - "Engineering"
permalink: /2024/02/02/bambulab-a1-3d-printer-review
image: /hugo-content/2024-02/nuculabs-dev-box.JPG
tags:
  - 3d
  - printer
  - freecad
  - cad
  - engineering
layout: post
published: false
---

![article cover image](/hugo-content/2024-02/nuculabs-dev-box.JPG)

Hello everyone, 👋

This is my first post of 2024, and ever since I've started the blog in ~2018, I've had
engineering ` Programming, Cloud and Engineering! 👨‍🔬;`
in the blog's description. 😄

I made a few posts related to "engineering" other than Software Engineering. In 2018, I was still studying computer
science and engineering at UPT, and I was fascinated by the Electrical Engineering courses. I figured that I'd write
blog posts about circuit analysis techniques such as Ohm's Law, Thévenin’s Theorem, Mesh Analysis, and so on.

Unfortunately, I did not write them and focused solely on Software Engineering and a few posts
on [Embedded](/categories/embedded/).

---

In this post, I talk about my experience with my first 3D printer the [Bambu Lab A1](https://bambulab.com/en/a1) 
and model real world objects using CAD software.

The printer is intuitive to use. I got my first print withing minutes after the initial setup and calibrations.

You don't need to perform manual calibration since the printer can self-calibrate before every print. On the first run,
it will do a more thorough calibration. Other than that, you will need to perform monthly or weekly maintenance, 
which is well documented on Bambu Lab's [website](https://wiki.bambulab.com/en/a1/maintenance).

The printer comes with software that runs on all the major platforms: Windows, macOS, Linux and Android. 

You can print everything from your phone (with fewer options) because the printer is connected to your Wi-Fi network.

As a beginner, I successfully printed:

- Ice Scrapper
- Cutlery Holder
- Raspberry PI case
- Phone Holder

While also modeling two objects by myself. A box for my audio interface and another box for the Raspberry PI case.

![ai-box.JPG](/hugo-content/2024-02/ai-box.JPG)

I've made this box so that I can glue my audio interface under my office.

![ai-box.JPG](/hugo-content/2024-02/rpi-case.jpg)

This is my Raspberry PI, which reads climate data from a BME680 sensors and displays it on my phone as well
as Grafana instance. I wrote more about this project in the [BME680 Home Assistant Integration
](/posts/2022/2022-01-16-bme680-home-assistant-integration/) post. I did not project the case from the image myself
but I made a small box that holds the case, which I've glued on the back of some furniture to hide the Raspberry. 

If you plan on buying a 3D printer yourself, you can find lots of free models on websites such as:

- [https://www.printables.com/](https://www.printables.com/)
- [https://makerworld.com/en](https://makerworld.com/en)

To model objects I've started, using a program called [Fusion 360](https://www.autodesk.com/products/fusion-360) and
I did not like the fact that it requires an active internet connection to use since all computations are done in the cloud.
Another reason I do not like this program is the cost. There is a free limited edition for personal use (for 3 years?),
but it is pretty costly for a hobbyist to buy a **license** for it.

Then I stumbled across [FreeCAD](https://www.freecad.org/), which is open-source and free to use! 🤯

![article cover image](/hugo-content/2024-02/freecad.png)