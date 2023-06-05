---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2022-02-13T23:12:30+00:00'
guid: https://nuculabs.dev/?p=2037
id: 2037
layout: post
permalink: /2022/02/13/how-i-got-my-first-pr-merged-into-apache-flink/
tags:
- apache flink
- apache software foundation
- Java
- opensource
- story
title: How I got my PR merged into Apache Flink
---
Hi 👋


This is a short story on how I got my pull request merged into Apache Flink.


It started with the need to set CPU and Memory limits to Flink jobs running under Kubernetes.


The first thing I did was to join the [**user** mailing list](https://flink.apache.org/community.html) and ask around if someone has encountered the issue and if there’s a solution to it. The people from the mailing list were very friendly and they pointed me to an existing ticket on the Flink[ jira board](https://issues.apache.org/jira/projects/FLINK/issues/), which was exactly what I needed.


To speed things up, I decided to implement the ticket by myself. I wrote on the mailing list that I want to implement [FLINK-15648](https://issues.apache.org/jira/browse/FLINK-15648) and started signing the Apache [individual contributor license agreement.](https://www.apache.org/licenses/contributor-agreements.html)


After sending the signed document via email, I cloned the Flink project from [GitHub ](https://github.com/apache/flink)and imported it into my IntelliJ IDE. Flink has some great [documentation ](https://nightlies.apache.org/flink/flink-docs-release-1.14/docs/flinkdev/ide_setup/)on how to setup your IDE and import the project.


Lastly, I’ve implemented the feature and submitted the PR [flink/pull/17098](https://github.com/apache/flink/pull/17098). The first time I forgot to generate the code docs and I’ve got a CI error. After the error was fixed, the PR was merged. It did not speed things up as I initially thought since it was merged into Flink 1.15. Nonetheless, It was a smooth and fun process and the code review that I’ve received was also well done.


I hope your experience contributing to open-source software will be as fun as mine was.


Thanks for reading and happy hacking!