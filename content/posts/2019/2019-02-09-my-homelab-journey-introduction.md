---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2019-02-09T21:45:45+00:00'
guid: https://nuculabs.wordpress.com/?p=335
id: 335
layout: post
permalink: /2019/02/09/my-homelab-journey-introduction/
tags:
- home automation
- slack app
title: 'My Homelab Journey: Introduction'
---
Hello everyone!


After following the [/r/homelab](https://www.reddit.com/r/homelab/) reddit for a while I’ve decided to give it a try and make myself my own homelab environment. I don’t have access to expensive server equipment nor do I have the means to acquire it, so I will be using an old HP Laptop which has quite a few resources, it is a bit stronger than the average Raspberry PI.


The laptop has 11GB of RAM memory, a 740GB disk and an Intel i7-4702MQ CPU. I have it for almost 4 years now and I’ve only used it for a few months, I decided to re-purpose it. If you’re asking yourself why didn’t I used the laptop, well, the laptop is absolutely horrible to work with! HP has made a terrible consumer product, it wasn’t targeted to business users and it is sluggish (with windows) and made of cheap plastic. On Windows HP has failed to provide basic working drivers, the laptop freezes every minute or so.


## The Busy Server


This will be the name of my new server Busy Server. The name was chosen because I plan to host all the services on this machine, for now.


For starting I’m going to put a DNS server, an OpenSSH server, an FTP server and a media server. In the future I’ll think about other services that might benefit me.


The DNS server’s purpose is to allow me to access the other resources in a more friendly manner, like `ftp.busyserver.net` instead if `192.168.0.*`


The FTP server will be an anonymous server to facilitate easy file sharing in the local network, it won’t have any outside access.


The media server will be unused, for now. A potential use case for it would be to get a Chromecast and use it to watch movies together with my family, but I’m not even sure if I need a media server.


## Cloud Services


I currently still rely on multiple cloud services for work.


There’s a few cloud services that I like a lot and I use them daily. I’m not ready to make the move from them to my own self-hosted alternatives. Some of these services are Trello, Slack, Dropbox, Github and Evernote.


Trello is a great tool for productivity, it gives me space to host my scrum boards, I don’t do any planning on it although I’ve heard about people that follow an agile lifestyle and plan their entire month using agile boards. I use Trello mostly to store programming notes and other technical stuff. I like the fact that I can invite other people to my boards and collaborate with them, but a huge turn-off is that it feels a bit weird to use when storing large notes, it’s not really meant for reading but I guess it could do for now. A replacement for it would be a 10$ Jira server license or another alternative.


Dropbox is the place where is store all the files that I need to take with me. Those files can be documents, books, software licenses and small programs. Of course they are encrypted with my personal GPG key so don’t even think about it.


While Slack is quite new to the scene, I’m thinking in making my own private work-space that I’ll share with my family members and perhaps close friends. In the future, I will attempt to compliment this with custom Slack apps, that will aid in home automation and status reporting, it’s going to be fun.


Evernote is my standard application for taking notes, it’s not perfect, but it works. I’m not going to pay for it (ever), I’d rather use simple markdown files if it goes this far. Evernote could be replaced with a MediaWiki or DokuWiki but since I rely on my notes a lot this won’t happen very soon.


Those are my plans for now, I also have a [nice VPN raspberry pi server setup](https://nuculabs.dev/posts/2018/04/22/how-to-run-your-own-openvpn-server-on-a-raspberry-pi/) that allows me to access my LAN network from outside.


What do you think about my plans? Do you I could make use of other interesting services? Let me know in the comments if so.


Thank you for reading and have a nice day!