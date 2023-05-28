---
author: "Denis Nu\u021Biu"
categories:
- Life
- Tutorial
date: '2022-03-06T21:42:10+00:00'
guid: https://nuculabs.dev/?p=2121
id: 2121
image: /wp-content/uploads/2022/02/image-7.png
layout: post
permalink: /2022/03/06/how-to-connect-ethernet-devices-to-wifi-via-wds/
tags:
- drivers
- ethernet
- Linux
- router
- tp link archer t4u
- ubuntu
- wds
title: How to connect Ethernet devices to WiFi via WDS
---
Hi **👋**,


In this blog post I will show you how to connect an Ethernet only device to Wi-Fi using an extra router and the WDS functionality. I initially wanted to install the TP Link Archer T4U WiFi adapter driver on my **Ubuntu 20.04** PC but unfortunately the driver is no longer supported.


Since I really needed high internet speed for my PC, I decided to connect it via an Ethernet cable and buy another **TP-Link** router to use in WDS mode.


WDS mode allows routers to communicate with one another without using wires.


⚙️ The configuration of the WDS router is as follows. I left the operation mode in Router Mode.


![](/wp-content/uploads/2022/02/image-7.png?w=804)
Then, I navigated to **Network** -> **LAN** and set the IP address to **192.168.0.2**. Because my old router had the IP address of 192.168.0.1.


![](/wp-content/uploads/2022/02/image-8.png?w=424)
The next step would be to navigate to **Wireless** -> **WDS**. Click survey and select the SSID of your root router. If the WDS bridging is successfully you should see a Connected message ✅.


![](/wp-content/uploads/2022/02/screenshot-from-2022-02-22-20-35-02.png?w=656)
Finally, go to **Network** -> **DHCP Server** and disable it. All these operations should be performed on the router that you want to bridge, not the 
root router
! 🕸️


Now that your router has a WDS bridge to the root router, you can connect your PC to it via an Ethernet cable and place it next to your PC. A brand new 5GHz Wi-Fi adapter might have the same price 💰 as a new router 🤑, and to be honest unless you’re doing some penetration testing I don’t see the need of a WiFi adapter.


Thanks for reading and happy hacking! 🐧