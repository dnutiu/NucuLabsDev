---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2019-04-14T20:15:48+00:00'
guid: https://nuculabs.wordpress.com/?p=367
id: 367
layout: post
permalink: /2019/04/14/quick-programming-tip-the-silver-searcher/
tags:
- cmd
- Tooling
title: 'Quick Programming Tip: The Silver Searcher'
---
[The Silver Searcher](https://github.com/ggreer/the_silver_searcher) is a code searching tool, it searches your entire code base in a very fast and efficient manner and it supports regex. I often use it when my IDE’s search capabilities are slow or can’t find what I’m looking for. You don’t have to use it exclusively, you can combine it with your IDE’s search capabilities to do your job faster, and most of the time it is also much easier to search using `ag`.


For example, In Pycharm, I can open up the integrated terminal (`ALT+F12`) and type `ag my-query` to get the list of files and lines which match my query, this is much faster than trying to search via the IDE.


Thanks for reading!