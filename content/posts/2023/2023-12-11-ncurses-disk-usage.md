---
title: "ncdu: NCurses Disk Usage"
author: [ "Denis Nutiu" ]
date: '2023-12-11'
categories:
  - "Tooling"
  - "Linux"
permalink: /2023/12/11/ncurses-disk-usage
image: /hugo-content/2023-12/ncdu.png
tags:
  - ncdu
  - tool
  - linux
layout: post
---

Hello everyone! 👋 

This is a short article about a handy tool called [ncdu](https://dev.yorhel.nl/ncdu).

The tool helps you delete files and directories from your filesystem. 
I use it daily to clear junk files from my workstation and the servers that I'm working on. 🧹

The way it works is quite simple:

- Navigate to the directory you want to clean and type `ncdu`.
- Select and delete files and directories.

![ncdu](/hugo-content/2023-12/ncdu.png)

Be **cautious** ⚠️ with what you're deleting. Once you delete a file, it's gone for real. 🗑️

If you want to see the full list of ncdu's commands you can type `?` while ncdu is open.

For installing ncdu I use `snap` on my Ubuntu machine and `homebrew` on my MacOS. 
To my knowledge there isn't any Windows version available yet.

I hope you've enjoyed this short article about ncdu! If you're looking for a great tool to replace 
your find and replace in the terminal check out my article about
[Fzf a command line fuzzy finder](/posts/2021/2021-09-22-fzf-a-command-line-fuzzy-finder/).

Thanks for reading and happy holidays! 🎄