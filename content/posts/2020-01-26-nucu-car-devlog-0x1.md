---
author: "Denis Nu\u021Biu"
categories:
- Embedded
- Programming
date: '2020-01-26T15:20:53+00:00'
guid: https://nuculabs.dev/?p=798
id: 798
layout: post
permalink: /2020/01/26/nucu-car-devlog-0x1/
tags:
- nucucar
- Raspberry Pi
title: 'Nucu Car: Devlog 0x01'
---
Hello everyone,


A while ago I started working on a pet project called NucuCar. It’s a platform written in .NET for the Raspberry Pi in which I’m trying to implement various utility functions to create a complete piece of software that can be used to build a remote controlled car for the Pi.


The project is open source and it can be found on GitHub: 


Since I didn’t buy a toy car and I don’t have much space in my apartment I was working only on the sensors module, which currently has support for the BME680 environment sensor and the CPU temperature sensor build in the Pi.


The idea behind this is to make a daemon that polls the sensor, publishes telemetry data and can be easily configured. This is the configuration file:


![](https://i0.wp.com/nuculabs.dev/wp-content/uploads/2020/01/nucucar_devlog_1_telemetry_config.png?fit=1024%2C590&ssl=1)
The **Telemetry** block configures which telemetry publisher to use *Azure* or *Disk* (for the moment, can be extended) and the interval in which to publish the telemetry data. The other two blocks configure whether the sensors and telemetry collection is enabled or not. That’s about it.


To develop this I’ve been using Github, a free Jira instance and JetBrains’ Rider.


![](https://i1.wp.com/nuculabs.dev/wp-content/uploads/2020/01/image.png?fit=1024%2C571&ssl=1)
- - - - - -


## Running on the Pi


I’ve build the project and uploaded it on the Pi, running it will all the options enabled yields the following **htop** result:


![](https://i0.wp.com/nuculabs.dev/wp-content/uploads/2020/01/image-1.png?fit=1024%2C524&ssl=1)
We have lots of processes which are in interruptible sleep, about 7.8% CPU time which went to 0.7% after a while and around 5.5MB or ram usage. That’s not bad, considering that we’re running C#. Oh, and I forgot to mention, the BME680 sensor is exposed via a gRPC server. 🙂


## Telemetry Data


While running the program, the telemetry data from the sensor was collected and saved to the disk. Here’s how an entry looks like:


```
```json
{
   "source":"NucuCar.Sensors",
   "timestamp":"2019-12-01T23:26:13.5537227+02:00",
   "data":[
      {
         "sensor_state":2,
         "temperature":32.65558333857916,
         "humidity":100.0,
         "pressure":62228.49565168124,
         "voc":0.0,
         "_id":"Bme680-Sensor"
      },
      {
         "sensor_state":2,
         "cpu_temperature":48.849998474121094,
         "_id":"CpuTemperature"
      }
   ]
}
```
```


Then I wrote a Python script to plot the available data, skipping the first 10 results.


![](/wp-content/uploads/2020/01/25884-test.png)
Thanks for reading!


### References:


- 
- 