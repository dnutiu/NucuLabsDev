---
author: "Denis Nu\u021Biu"
categories:
- Embedded
- Programming
date: '2020-04-19T17:26:17+00:00'
guid: https://nuculabs.dev/?p=915
id: 915
image: /wp-content/uploads/2020/04/64586-nucucarovervewdiagram.png
layout: post
permalink: /2020/04/19/nucu-car-devlog-0x02/
tags:
- iot
- nucucar
- Raspberry Pi
title: 'Nucu Car: Devlog 0x02'
---
Hello,


I’m still working on my NucuCar project from time to time, I’m currently at my parents home and I left the raspberry pi at my apartment so I’m only testing the builds it on my computer. Luckily I can test the telemetry by using the CPU temperature sensor implemented by dotnet IoT.


For the next step, I wanted an effective way to store telemetry data in the cloud, preferably for free, since this is a hobby project after all.


After testing multiple solutions I’ve settled with Firebase, I tried to write a telemetry publisher for it using it’s SDK which internally uses gRPC and everything went well until I put it on the board. It turns out that dotnet gRPC doesn’t work on raspberry pi. I got the basic server working using a hack but that’s about it, so I moved to the REST API and I’ve implemented a simple translator which translates raw C# dictionaries into compatible Firebase RESTful post request Json payload.


The project got a little bigger and at the current state it was quite messy, a restructuring is needed and this is what I’ve done:


![](/wp-content/uploads/2020/04/64586-nucucarovervewdiagram.png)
## **NucuCar.Domain**


Initially I’ve designed the NucuCar.Domain project to contain **.proto** files and data classes without too much implementation, however, many Telemetry Publishers such as Azure, Disk were in this package and it was kind of wrong because they contain too much implementation and they were dependent on heavy external packages and they would bring too much of a burden.


The **TelemetryPublisher** abstract class and the **ITelemeter** interface both which don’t contain implementation remained in NucuCar.Domain and the concrete publishers along with the worker were extracted in NucuCar.Telemetry.


I’ve also extracted the Firebase translator into a package of it’s [own](https://github.com/dnutiu/FirebaseRestTranslator), and published it on [NuGet](https://www.nuget.org/packages/FirebaseRestTranslator/), making this my first published package. 😀


## **NucuCar.Common**


In NucuCar.Common, I’ve moved some of the classes which were utility classes and were still present in NucuCar.Domain, the ConnectionParser class and a class which provides basic argument guarding.


Another thing that I needed and I’ve implemented in NucuCar.Common is a HttpClient wrapper class, I found very verbose to serialize Json every time and deserialize it when sending data to Firebase.


Thus I made a simple wrapper to simplify the process, while also introducing a retry mechanism and cancellation and timeout support.


## Conclusion


Overall I’m quite happy with the end result and I’m looking forward to see what interesting things I can implement in the future.


I’m not sure if I want this to be a remote controlled toy car project anymore, it sounds good but I think it would be more practical If I could connect multiple sensors, create a simple interface and monitor my apartment using this, perhaps add some support to automatically water my plants, that would be nice.


 The project is open source and can be found on my [Github](https://github.com/dnutiu/NucuCar).


Thanks for reading!