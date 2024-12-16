---
author: "Denis Nutiu"
categories:
  - Tutorial
  - Video
date: '2024-12-16'
layout: post
permalink: /2024-12-16-how-to-install-redis-on-synology-nas
tags:
  - redis
  - synology nas
  - container manager
  - docker
  - install
  - how-to
title: How to install Redis on Synology NAS (Container Manager)
---

![DaVinci Helper](/hugo-content/2024-12/RedisOnNAS.png)

Hello everyone! 👋

I run a small set of containers on my NAS at Home to monitor different weather and run automations. Because of this I
wanted to install Redis on my NAS as well.

Redis is a key value database that has a lot of extra modules such as:

- Redis Search:  A query and indexing engine for Redis, providing secondary indexing, full-text search, vector
  similarity search and aggregations.
- Redis JSON: A NO-SQL Document Database
- Redis Graph: A graph database.
- Redis TimeSeries: A time series database.
- Redis Bloom: Bloom and Cuckoo filters implementation.
- Redis Streams: An append only Log

These functionalities save me a lot of time and improve my software running inside my home. Redis is also a simple
service to deploy and maintain.

So I made a quick tutorial on how to install [Redis](https://redis.io/) on
a [Synology NAS](https://www.synology.com/en-global)
using the Container Manager.

{{< youtube e61r5arEGJU >}}