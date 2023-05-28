---
author: "Denis Nu\u021Biu"
categories:
- Tutorial
- Windows
date: '2021-10-02T15:25:08+00:00'
guid: https://nuculabs.dev/?p=1690
id: 1690
layout: post
permalink: /2021/10/02/running-linux-gui-applications-in-windows/
tags:
- forwarding
- gui
- wsl
- x-server
title: Running Linux GUI Applications in Windows
---
Hi 👋,


This is a quick tutorial on how to run Linux graphical interface application in Windows using X Server’s forwarding feature.


The first step is to download and install the [VcXsrv Windows X Server](https://sourceforge.net/projects/vcxsrv/) on your 💻 Windows machine.


Then, start [VcXsrv](https://sourceforge.net/projects/vcxsrv/) with the following configuration:


- 
![](/wp-content/uploads/2021/09/image-5.png?w=569)
- 
![](/wp-content/uploads/2021/09/image-6.png?w=573)
- 
![](/wp-content/uploads/2021/09/image-7.png?w=535)
- 
![](/wp-content/uploads/2021/09/image-8.png?w=540)


Ensure that VcXsrv is not blocked in Windows Firewall, it should be allowed in **Public** and **Private** networks.


![](/wp-content/uploads/2021/09/image-10.png?w=706)
You may also use more restrictive firewal settings as explained in [/wsl-windows-toolbar-launcher#firewall-rules](https://github.com/cascadium/wsl-windows-toolbar-launcher#firewall-rules).


- - - - - -


The next step is to configure your Linux box.


I’m using Ubuntu in WSL 2.0 and pasting the following code into **~/.zshrc** or **~/.bashrc** does the trick for me. Feel free to upvote the author on [SO](https://stackoverflow.com/a/65576810/4092044).


```
# X11
HOST_IP=$(host `hostname` | grep -oP '(\s)\d+(\.\d+){3}' | tail -1 | awk '{ print $NF }' | tr -d '\r')
export LIBGL_ALWAYS_INDIRECT=1
export DISPLAY=$HOST_IP:0.0
export NO_AT_BRIDGE=1
export PULSE_SERVER=tcp:$HOST_IP
```


After that’s done, if VcXsrv is running, you should be able to open Linux GUI apps from your Terminal.


![](/wp-content/uploads/2021/09/image-9.png?w=1024)
Thanks for reading! 🍺