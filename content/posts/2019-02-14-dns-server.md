---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2019-02-14T17:57:15+00:00'
guid: https://nuculabs.wordpress.com/?p=342
id: 342
layout: post
permalink: /2019/02/14/dns-server/
tags:
- dns
- dnsmasq
title: Setting up a simple DNS server
---
Right after I’ve configured [the FTP server](https://nuculabs.dev/posts/2019/02/11/homelab-installing-vsftpd/), I wanted to be able to “name” my server and so I’ve installed a lightweight DNS server. (context:[ previous article](https://nuculabs.dev/posts/2019/02/11/homelab-installing-vsftpd/))


> “**dnsmasq** is a lightweight DNS, TFTP, PXE, router advertisement and DHCP server. It is intended to provide coupled DNS and DHCP service to a LAN.”


I’ve chosen this over the bind 9 dns server because it was very easy to setup, I almost got it right the first time.


To install and configure dnsmasq, you’d usually do the following:


```
# On Ubuntu
sudo apt-get install dnsmasq
sudo cp /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
```


And override **/etc/dnsmasq.conf** with this configuration:


```
#/etc/dnsmasq.conf
domain-needed
bogus-priv
expand-hosts
 
# The address 192.168.0.176 is the static IP of this server 
# You can find this ip by running ifconfig and look for the 
# IP of the interface which is connected to the router.
listen-address=127.0.0.1
listen-address=192.168.0.176
bind-interfaces
 
# Use open source DNS servers
server=8.8.8.8
server=8.8.4.4
server=208.67.220.220
 
# Create custom 'domains'.
# Custom 'domains' can also be added in /etc/hosts
address=/busyserver.net/192.168.0.176
```


And that’s basically it.


You can check the status of dnsmasq server by running: `sudo systemctl status dnsmasq`.


To actually use the server and make your computer recognize your custom domains you need to tell your computers to use the IP of the server on which dnsmasq is installed, in my case it is 192.168.0.176.


Thank you for reading!


[Twitter](https://twitter.com/denisnutiu) | [DORefferal](https://m.do.co/c/22f012126c25)