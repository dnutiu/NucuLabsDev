---
author: "Denis Nu\u021Biu"
categories:
- Linux
- Tooling
- Windows
date: '2021-09-22T22:18:40+00:00'
guid: http://nuculabs.dev/?p=1697
id: 1697
image: /wp-content/uploads/2021/09/pexels-photo-1382393.jpeg
layout: post
permalink: /2021/09/22/fzf-a-command-line-fuzzy-finder/
title: "fzf &#8211; A command-line fuzzy finder \U0001F338"
---
Hi 👋,


fzf is a command line tool that can be used to fuzzy search files, history, and command outputs. Check out the [Github](https://github.com/junegunn/fzf) repository.


This is a tool that I wish I’ve knew about earlier in my career, and for this reason I decided to blog about it.


![](/wp-content/uploads/2021/09/image-3.png?w=1024)
You can also set key bindings; some popular ones are:


`
CTRL-T
` – Paste the selected files and directories onto the command-line


`
CTRL-R
` – Paste the selected command from history onto the command-line


`
ALT-C
` – cd into the selected directory


If you’re on Linux and want to try it, then installing it using [Git](https://github.com/junegunn/fzf#using-git) is straight forward and it also sets up your shell and key bindings.


On Windows, to enable the key bindings you can install the PSFzf module by running **Install-Module PSFzf** in an admin Powershell.


Edit your $PROFILE file and add the following items:


```
Remove-PSReadlineKeyHandler 'Ctrl+r'
Remove-PSReadlineKeyHandler 'Ctrl+t'
Import-Module PSFzf
```


![](/wp-content/uploads/2021/09/image-4.png?w=1024)
Thanks for reading! 🍻