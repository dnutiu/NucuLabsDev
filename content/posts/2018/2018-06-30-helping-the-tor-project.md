---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2018-06-30T07:40:04+00:00'
geo_public:
- '0'
- '0'
- '0'
- '0'
guid: https://nuculabs.wordpress.com/?p=80
id: 80
layout: post
permalink: /2018/06/30/helping-the-tor-project/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- tor
title: Helping the Tor Project
---
In this short article I will try to introduce you to the Tor Project and  
what can you do in order to help it.


### What is the Tor project?


The EFF describes the Tor project as:


> Tor is a service that helps protect your anonymity while using the Internet by obfuscating your online behavior and by obscuring your identity from unwanted surveillance from other users,  
> governments, or corporations. When you use the Tor software, your IP address remains hidden, and it appears that your connection is coming from the IP address of a Tor exit relay,  
> which can be anywhere in the world. Tor also allows access to .onion hidden services, which allow people to publish information while hiding their location.


Tor is comprised of two parts:


- software you can download that allows you to use the Internet anonymously (the Tor Browser  
    Bundle)
- volunteer network of computers that make it possible for the software to work.


### What is a relay?


A relay is a computer that proxies connections. Here’s the definition of a relay from google:


> receive and pass on (information or a message).


When you connect to Tor, you’re connecting to a relay which encrypts your connection and passes it to another relay, this goes on and on until you reach the **exit** relay.  
The exit relay makes a unencrypted connection to the resource you requested and passes it back to the former relay.


There are two types of relays:


- Middle relay
- Exit relay


If you want to run a relay, I suggest you run an middle relay because all you need to do is handle encrypted traffic. On the other hand, if you run an exit relay  
you will handle unscripted traffic and you will be the source of that traffic, since you’re the exit.  
If somebody uses Tor to do bad things your exit relay’s IP address will be listed as the source of the traffic and if people don’t know that you’re running a tor  
relay you may get a visit from the police and perhaps some trouble. Middle relays are safe.


### How can you run a relay?


You can run a relay on your own computer or on a computer located in the cloud, all you need to do is follow the [TorRelayGuide](https://trac.torproject.org/projects/tor/wiki/TorRelayGuide)  
which contains all the info you need..


In my experience, I didn’t configure my first relay correctly and I had to reinstall the VPS and try again. It’s fine if you don’t get it at first, just keep trying.


All I had to do, really is run a simple shell [script](https://github.com/coldhakca/tor-relay-bootstrap) and after that I’ve installed [Nyx](https://nyx.torproject.org/) a command-line utility for monitoring and controlling your relay.


To install it, on Debian you run the following commands:


\[code lang=”bash”\]  
sudo apt-get install python3 python3-pip  
pip intall nyx  
\[/code\]


And then you access it using `nyx`


Other things you can do after you installed Nyx is changing the default ports and limiting the bandwidth.


To change the badwidth just uncomment AccountingMax and AccountingStart from `/etc/tor/torrc/`


To change the ports just change ORPort and DirPort to whatever you like, I recommend the following setup.


\[code lang=”text”\]  
ORPort 443  
DirPort 80  
\[/code\]


Then add the following rules to iptables for IPv4 and IPv6


\[code lang=”text”\]  
-A INPUT -p tcp –dport 443 -j ACCEPT  
-A INPUT -p tcp –dport 80 -j ACCEPT  
\[/code\]


Also, update your contact info in `torrc` so people can contact you in case you didn’t configure the relay correctly.


That’s it! Have fun ^\_^


Also, check out other ways to [volunteer](https://www.torproject.org/getinvolved/volunteer.html.en) your time to the Tor Project and how can you get a cool [T-Shirt](https://www.torproject.org/getinvolved/tshirt.html).


Feel free to follow me on Twitter. 🙂