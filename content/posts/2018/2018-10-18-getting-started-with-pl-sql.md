---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2018-10-18T19:59:00+00:00'
guid: https://nuculabs.wordpress.com/?p=234
id: 234
layout: post
permalink: /2018/10/18/getting-started-with-pl-sql/
tags:
- docker
- oracle
- sql
title: Getting started with PL/SQL
---
I signed up for the Database Design course at my university, the courses are great but the practical laboratories are not, it’s just a bunch of tiresome SQL queries and incomplete instructions. If you too have to do a lot of tiresome SQL queries I suggest the following setup:


1. Download [Docker](https://www.docker.com/).
2. Download any [Intellij IDE](https://www.jetbrains.com/idea/) (if you’re a student you can get it for free). Preferably [DataGrip](https://www.jetbrains.com/datagrip/).


After you installed the aforementioned tools you start with a some docker commands:


Instructions [here](https://github.com/wnameless/docker-oracle-xe-11g), if you want to explore on your own.


```
docker pull wnameless/oracle-xe-11g
docker run -d -p 49161:1521 wnameless/oracle-xe-11g
```


Now, open DataGrip, add a new data source, select Oracle and use the following settings:


```
hostname: localhost
port: 49161
sid: xe
username: system
password: oracle
```


Do not bother with the official docker images from Oracle, they usually don’t work.


Thank you for reading!