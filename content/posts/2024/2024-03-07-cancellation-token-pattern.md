---
title: "Cancellation Token Pattern with Python"
author: [ "Denis Nutiu" ]
date: '2024-03-07'
categories:
- Python
- Programming
permalink: /2024/03/07/cancellation-token-pattern-with-python
tags:
  - python
  - programming
  - design patterns
layout: post
published: false
---

Hello! 👋

I wanted to write this article for a while now but I've postponed it. The Cancellation Token Pattern article is a pattern
that is inspired by C#'s [CancellationToken](https://learn.microsoft.com/en-us/dotnet/api/system.threading.cancellationtoen?view=net-8.0) struct and Golang's
[context](https://pkg.go.dev/context) package.

The main idea of the pattern is to **allow the user of an API** to cancel its operations, but in order 
for this pattern to work the API must be written with a cancellation token in mind.


