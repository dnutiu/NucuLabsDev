---
title: "Projects"
layout: "single"
author: "Denis Nutiu"
showtoc: true
---

Hello there! 👋

This page servers as a quick showcase of my projects that I have done them in my free time for fun, university 
or to learn new skills.

I'm a builder at heart, and I like building things. 😄

## NucuLabs.Dev


**When**: Feb 2018 - *Present*

**Story**: This is my personal blog that I've started to improve my writing skill and share my knowledge with the world.

I wrote my first article [How to get started with FreeRTOS and ESP8266](https://nuculabs.wordpress.com/2018/02/03/how-to-get-started-with-freertos-and-esp8266/#more-24)
initially on my wordpress-based blog, and since then I moved the blog to a self-hosted static site generator 
[Hugo](https://gohugo.io/) based blog deployed on GitHub Pages. 

Some of my articles were also published by [FreeCodeCamp](https://www.freecodecamp.org/news/how-to-get-started-with-freertos-and-esp8266-7a16035ddd71/). 🎉

**Tech Stack**: Markdown, Hugo, Github Pages


## Hobby Projects

Projects that I've done out of passion during my free time. 

---
### Image Tagger ([GitHub](https://github.com/dnutiu/image-tagging))

**When**: Jul 2023

**Story**: As an amateur photographer 📸 that sometimes uploads photos online on [Unsplash](https://unsplash.com/@ndcphoto),
I find it boring and tedious to find tags for the images.

And while I was working on the [Practical Deep Learning](https://course.fast.ai/) course, I learned to train and make
deep learning models. 

Using this new knowledge, I built this **cross-platform** Desktop tool with C# and [Avalonia UI](https://avaloniaui.net/).

To train my model, I've used Python and [fast.ai](https://www.fast.ai/) and to get training data, I've built a performant web scrapper that evades
detection using Python. I haven't published the scrapper code yet.

![Image Tagger](https://github.com/dnutiu/image-tagging/raw/master/docs/preview.png)

**Tech Stack**: C#, Python, Deep Learning

---
### Jekyll To Hugo ([GitHub](https://github.com/dnutiu/jekyll-to-hugo))

**When**: May 2023

**Story**: This is a niche tool that I've built to move my blog from WordPress to a 
self-hosted static site powered by Hugo on GitHub pages.

The initial version was written in 2 hours while I was commuting by train in Romania. It's far from perfect but it worked
well enough for my use case.

It's published on pipy as a [Python package](https://pypi.org/project/jekyll-to-hugo/0.0.9/) and, 
the documentation is published on [GitHub pages](https://dnutiu.github.io/jekyll-to-hugo/).

I've also made a [video](https://youtu.be/wFkPMxhwoK8) that showcases the tool.

**Tech Stack**: Python

---
### Speedy ([GitHub](https://github.com/dnutiu/speedy))

**When**: Apr 2022

**Story**: This project acts as an integration bridge between [Loki](https://grafana.com/oss/loki/) and [Apache Kafka](https://kafka.apache.org/).

We needed to move data from Apache Kafka into Loki, and this project streams the data from Kafka to Loki. 

Once deployed, it will consume JSON data from a Kafka topic, flatten it and insert it into Loki.

**Tech Stack**: Go, Docker, Kubernetes, Loki, Apache Kafka

---
### Image Metadata Remover ([GitHub](https://github.com/dnutiu/ImgMetadataRemover))

**When**: Apr 2022

**Story**: Images contain metadata information such as GPS Data and the device used to take the picture. 

Since I enjoy posting pictures online, I developed this command line application to remove the metadata from images 
so that they can be safely uploaded online.

This project was a nice learning experience with C#.

**Tech Stack**: C#

---
### Bme680 HomeKit ([GitHub](https://github.com/dnutiu/bme680-homekit))

**When**: Feb 2022

**Story**: [Bme680](https://shop.pimoroni.com/products/bme680-breakout?variant=12491552129107) is an air quality sensor developed by Bosh.

I used it to measure my home's temperature and humidity from my iPhone. This project turned my [Raspberry Pi](https://www.raspberrypi.com/)
into a [HomeKit](https://www.apple.com/home-app/) accessory, and because of that I can always view the temperature 
on my phone even when I'm not at home, thanks to Apple's [home hub](https://support.apple.com/en-us/HT207057) features.

Since the project runs on Raspberry Pi, I've installed a Grafana instance to visualize the historical data from my computer.

![Grafana](https://github.com/dnutiu/bme680-homekit/blob/master/docs/grafana_dashboard.png?raw=true)

**Tech Stack**: Python, Linux, Raspberry Pi

---
### Library for PMS5003 ([GitHub](https://github.com/dnutiu/pms5003))

**When**: Apr 2021

**Story**: PMS comes from particulate matter sensor and [PMS5003](https://shop.pimoroni.com/products/pms5003-particulate-matter-sensor-with-cable?variant=29075640352851)
is a relatively economical sensor for measuring air quality.

I have implemented an intuitive library in C# to read data from the sensor using the [serial bus](https://en.wikipedia.org/wiki/Serial_communication).

**Tech Stack**: C#

---
### Retroactiune ([GitHub](https://github.com/dnutiu/retroactiune))

**When**: Nov 2019 - May 2021

**Story**: Retroactiune is a web application built with .NET Core, it is an end-to-end system for managing anonymous feedback. 

The goal of this project was to enable its users to collect anonymous feedback. 

Let's say you are a teacher and want to collect feedback for your course.  You would generate a set of **tokens**, then give them to your students.
A student can use a token to leave feedback for your course.

It currently lacks a user interface and only APIs are implemented.

**Tech Stack**: C#, ASP.NET Core, MongoDB

![Retroactiune high level overview](https://github.com/dnutiu/retroactiune/raw/main/docs/deploy_architecture.png)

---
### Nucu Car ([GitHub](https://github.com/dnutiu/NucuCar/tree/master))

**When**: Nov 2019 - May 2021

**Story**: This was an odd project, with the release of [.NET IoT](https://dotnet.microsoft.com/en-us/apps/iot) I wanted
to implement [my friend's project](https://github.com/vnemes/PiCar) with using dotnet on a Raspberry Pi, then I dropped
the `car` part and focused entirely on a platform for reading and visualizing sensor data.

I've developed the entire project using C#, it featured a telemetry module that collected data from sensors and published it on Microsoft Azure's [IoT Hub](https://azure.microsoft.com/en-us/products/iot-hub). 

A [gRPC](https://grpc.io/) (Remote Procedure Call) server for exposing data to local clients, a [circuit diagram](https://github.com/dnutiu/NucuCar/tree/master/NucuCar.Sensors).

It was fun cross-compiling the project for ARM.

To visualize sensor data I've developed an [Android application](https://github.com/dnutiu/NucuCar/tree/master/NucuHub.Android).

**Tech Stack**: C#, Microsoft Azure, Linux, Raspberry PI

![Android app](https://github.com/dnutiu/NucuCar/raw/master/Docs/images/nucucar_android.png)

---
### Snowpiercer Game ([GitHub](https://github.com/dnutiu/projekt-bombs))

**When**: Jun 2019 - Sep 2019

**Story**: I've worked on this game with my friends, we built it to teach us Unity. It was inspired by the popular
[Dyna Blaster](https://en.wikipedia.org/wiki/Dynablaster) game. 

Out goal was to become game developers and start a game studio 🚀. 
Turns up game development is way harder that we thought, 😅 and the project remained unfinished 💥.

If you're curious about this game you can play it in your [web browser](https://dnutiu.itch.io/snowpiercer-isog).

We also got an Android application published on the Google Play but we didn't release it to the public. 

**Tech Stack**: Unity, C#

---
### Rock Paper Scissors ([GitHub](https://github.com/dnutiu/js-rockpaperscissors))

**When**: **When**: *Approximately in August -2015.*

**Story**: Another simple game built for the web browser. 🪨📃✂️

**Tech Stack**: JavaScript, HTML, CSS, jQuery

---
### Hangman ([Github](https://github.com/dnutiu/ro-hangman))

**When**: *Approximately in January -2015.*

**Story**: As I was learning Git, GitHub and Web Development I have developed a small Hangman game using JavaScript. 

It featured a full layout for desktop web browsers and a mobile layout. It also played different sounds when the player
lost or won the game.

**Tech Stack**: JavaScript, HTML, CSS

---

## University Projects

Here are a few examples of projects I've successfully completed as part of my university coursework at [UPT](https://ac.upt.ro/en/).

### simplFT ([Github](https://github.com/dnutiu/simplFT))

**When**: Oct 2018 - Oct 2018

**Story**: This was the final project for my **Computer Networks** class. It is a simple and concurrent file transfer server.
A user would interact with it using the `nc` (netcat) unix command line utility to upload and download files from the server.

While working on this project, I also had the opportunity to debug it using [WireShark](https://www.wireshark.org/), and
I've also had TravisCI (Continuous Integration) set up for running unit-tests.

**Tech Stack**: Go, Docker

---
### benchmark-scoreboard ([Github](https://github.com/dnutiu/benchmark-scoreboard))

**When**: Apr 2017 - Apr 2017

**Story**: This project was done for the **Computer Organization** class. We were a small team of four students, 
and the goal was to build a benchmarking tool. I was in charge of building the website and server for displaying the 
benchmark results in a web browser. My teammates were responsible for the benchmarking Desktop application and designed benchmarks scenarios. 

I used Python and Flask for the backend server and Twitter Bootstrap for the UI. For the database, 
I used MySQL and [deployed](https://github.com/dnutiu/benchmark-scoreboard/wiki/Deployment) the backend on a Linux VPS.

**Tech Stack**: Python, Flask, Linux