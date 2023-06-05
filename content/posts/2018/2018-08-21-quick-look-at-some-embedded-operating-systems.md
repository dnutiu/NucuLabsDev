---
author: "Denis Nu\u021Biu"
categories:
- Embedded
date: '2018-08-21T21:43:52+00:00'
guid: https://nuculabs.wordpress.com/?p=189
id: 189
layout: post
permalink: /2018/08/21/quick-look-at-some-embedded-operating-systems/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- Linux
- operating systems
- rtos
title: A quick look at some Embedded Operating Systems
---
## Real Time Operating Systems


Real time operating systems are designed for real time applications that need to accomplish a certain tasks with as little OS overhead as possible. Tasks like reading the sensors and displaying data.


You could write an infinite while loop to accomplish that but things will get complicated once you have multiple tasks. RTOS provide users with a task scheduler and several methods for synchronizing tasks and inter-task communication, as well as other stuff, not necessarily related to tasks.


The task scheduler allocates CPU time for the tasks. Tasks can be prioritized, a high priority task that never stops will eat all the available CPU time and won’t let any other tasks to run. You can write your own scheduling algorithm or you can use the one provided by the OS. The algorithm can be *preemptive* or *non-preemptive*. Preemptive algorithms interrupt tasks and resumes them at a later time and non-preemptive algorithms wait until a task has finished executing.


There are a lot of real time operating systems in the market but the most popular is [FreeRTOS](https://www.freertos.org/).


In 2016 the creator of FreeRTOS has joined Amazon and [Amazon FreeRTOS](https://aws.amazon.com/freertos/) was born, which is like FreeRTOS but with AWS integrations.


More: [Amazon FreeRTOS](https://aws.amazon.com/freertos/)


**MbedOS** is another modern RTOS designed with IoT in mind, it is developed by ARM and supports only ARM Cortex-M microprocessors.


More: [Mbed OS](https://www.mbed.com/en/platform/mbed-os/https://www.mbed.com/en/platform/mbed-os/)


## Embedded Linux


Embedded Linux operating systems takes advantage of the stability and robustness provided by the Linux kernel along with the many free open source packages that allow a great amount of flexibility and code reusability.


Some of the distributions are:


[Android ](https://developer.android.com/)– An OS based on the Linux kernel designed for mobile devices.


[OpenWrt](https://openwrt.org/) – An Embedded Linux distribution designed for devices that route network traffic, like wireless routers.


[uCLinux ](http://www.uclinux.org/)– Is a port of Linux but without the Memory Management Unit.


## Windows IoT Core


Windows 10 IoT Core is Microsoft’s take on IoT devices. It’s Windows 10 but optimized for small devices. It can run Universal Windows Platforms applications and it integrates nicely with Azure IoT services.


More: 