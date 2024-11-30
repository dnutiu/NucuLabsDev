---
author: "Denis Nutiu"
categories:
- Tutorial
date: '2024-11-30'
layout: post
permalink: /2024/11/30/2024-11-30-how-to-install-davinci-resolve-on-linux-fedora/
tags:
- audio fix
- audio not working
- davinci resolve
- fedora
- Linux
- install
- how-to
title: How to install DaVinci Resolve on Linux (Fedora Edition)
---

Hello everyone! 👋

In this article you will learn how to install DaVinci Resolve Free and Studio editions on Fedora 40 and Fedora 41.

The first step is to download DaVinci Resolve from the official website, either with:

- [Free Edition](https://www.blackmagicdesign.com/products/davinciresolve/)
- [Studio Edition](https://www.blackmagicdesign.com/support/family/davinci-resolve-and-fusion)

The next step is to download and install [DaVinci Helper](https://github.com/H3rz3n/davinci-helper).

```shell
sudo dnf copr enable -y herzen/davinci-helper
sudo dnf install -y davinci-helper
```

Once you've installed DaVinci Helper, the following steps are simple. Simply circle through the tabs to on the left
and select the DaVinci Resolve zip file when prompted.

![DaVinci Helper](/hugo-content/2024-11/davinci-helper.png)

After all the steps are done, you can open DaVinci Resolve:

![DaVinci Resolve](/hugo-content/2024-11/davinci-resolve-working.png)

## Audio Interface

I use a Rode AI-1 audio interface and I wrote an article about it how to get it working properly on Linux.

You can read it:
- [How to make RØDE audio interface work on Linux](/posts/2022/2022-02-21-how-to-make-rode-audio-interface-to-work-on-linux/).

Thank you reading! 🐧