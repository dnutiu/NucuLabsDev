---
author: "Denis Nu\u021Biu"
categories:
- Embedded
- Programming
date: '2019-11-09T19:26:34+00:00'
guid: https://nuculabs.wordpress.com/?p=543
id: 543
layout: post
permalink: /2019/11/09/my-experience-with-net-iot-so-far/
tags:
- dotnet
- iot
- nucucar
- Raspberry Pi
title: My experience with .NET IoT (so far)
---
[![cover](https://res.cloudinary.com/practicaldev/image/fetch/s--sYwzOfEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8680jyx9l0y8fbgeir2.jpg)](https://res.cloudinary.com/practicaldev/image/fetch/s--sYwzOfEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8680jyx9l0y8fbgeir2.jpg)
I haven’t done any embedded development in a while and I was thinking to build myself a remote controlled toy car with video streaming.


The project is going to take me a while. To build it I’m going to use C#. I’m already too familiar with Python and a little challenge doesn’t hurt.


To interface with the hardware, I’m going to use the [.Net Core IoT Libraries](https://github.com/dotnet/iot) from Microsoft.


- - - - - -


My idea was to create a set of [.Net Core](https://dotnet.microsoft.com/download) services that communicate internally via [gRPC](https://grpc.io/) and an ASP.Net Core web application to control the car. (Now that I’m writing this I’m thinking that could possibly drop the web app and use gRPC directly from the client).


The client would be a desktop application that will display the video stream and data from the sensors. It should work with an Xbox controller or else it won’t be fun. 🙁


To write it I will probably use [Mono](https://www.mono-project.com/) or [Unity](https://unity.com/).


- - - - - -


After burning the latest version of Raspbian on the board using [Etcher](https://www.balena.io/etcher/) and spending a few hours in [Rider](https://www.jetbrains.com/rider/) developing a base for the app. I’ve Installed .Net Core 3 SDK and ran the application. The results were amazing! I’ve checked the thermostat in the office and it was set on 25


[![readings](https://res.cloudinary.com/practicaldev/image/fetch/s--VTUaHff9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4p9r02yigi8ky2jczk10.jpg)](https://res.cloudinary.com/practicaldev/image/fetch/s--VTUaHff9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4p9r02yigi8ky2jczk10.jpg)
And the application only consumes ~10MB RAM! That’s awesome!


- - - - - -


I’m really impressed and so far things are looking bright for `dotnet/iot`. I can’t wait to test the gRpc support and other hardware modules.


The full code can be found here: 


If you want to run the app on your Raspberry you’ll need to install the .Net Core Sdk ([Instructions](https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-3.0.100-linux-arm32-binaries)) and run the following commands:


```
git clone https://github.com/dnutiu/NucuCar.git
cd NucuCar
dotnet build
cd NucuCar.Sensors
dotnet run
```


- - - - - -


Thanks for reading!


I hope you will give `dotnet/iot` a try for your next project!