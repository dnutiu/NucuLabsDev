---
author: "Denis Nu\u021Biu"
categories:
- Embedded
date: '2018-07-09T16:17:03+00:00'
guid: https://nuculabs.wordpress.com/?p=118
id: 118
image: /wp-content/uploads/2018/07/screen-shot-2018-07-08-at-00-22-47.png
layout: post
permalink: /2018/07/09/ble-ctf-how-to-flash-the-esp32/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- ble
- CTF
- esp32
- flash
title: 'BLE CTF: How to flash the ESP32'
---
Hello!


If you want to do [Hackgnar’s](https://twitter.com/hackgnar) [BLE CTF](http://www.hackgnar.com/2018/06/learning-bluetooth-hackery-with-ble-ctf.html) but you’ve been struggling with flashing the ESP worry not! I have created a vagrant developer environment just for this.


The environment can be found here: 


To use it you need to have [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/) installed.


After you got those installed, clone the repo and run the following commands on the terminal. This will start up the VM and ssh into it.


\[code language=”bash”\]  
vagrant up && vagrant ssh  
\[/code\]


This will take a while, there’s a lot of dependencies that have to be installed.  
After the command has completed you should now be logged in into the vagrant machine. Now plug in your ESP32 and make sure that it’s recognised, run **lsusb** to list all devices and check for **/dev/ttyUSB0**, if that isn’t present then flashing won’t work. The last step is to flash.


\[code language=”bash”\]  
cd ble\_ctf  
make flash  
\[/code\]


![Screen Shot 2018-07-09 at 18.41.47](/wp-content/uploads/2018/07/screen-shot-2018-07-09-at-18-41-47.png)


I only tested this setup on my own machine. It might not detect all boards automatically, because the boards are made by different manufacturers and I did not fingerprint them all (see Vagrantfile). In case your board is not detected, an easy fix is to fire up VirtualBox, login with vagrant:vagrant and right click settings -> USB -> add new usb device.


Check my [How to get started with FreeRTOS and ESP8266 ](https://nuculabs.dev/posts/2018/02/03/how-to-get-started-with-freertos-and-esp8266/) post as it has more details on how to do that.


If the board is not seen by your operating system then there are several things you could try:


- Try different USB cables. Some cables support only power transfer.
- Try installing the driver required by the board’s serial communication chip.
- Make sure your board is not broken. :/


## Happy hacking!