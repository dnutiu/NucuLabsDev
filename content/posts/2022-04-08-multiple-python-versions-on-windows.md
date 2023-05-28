---
author: "Denis Nu\u021Biu"
categories:
- Windows
date: '2022-04-08T22:58:18+00:00'
guid: https://nuculabs.dev/?p=2230
id: 2230
layout: post
permalink: /2022/04/08/multiple-python-versions-on-windows/
tags:
- multiple python versions
- Python
title: Multiple Python versions on Windows
---
Hi 👋


In this short article I will show you two ways of changing Python versions on Windows. It is useful when you have installed multiple Python versions on your system and want to run a specific version from the terminal.


For example, if we have the following versions installed:


![](/wp-content/uploads/2022/04/image.png?w=812)
We can use either the Python Launcher `py` to run Python or the `python` command.


## Python Launcher


To list installed Python versions with Python launcher we can use the `py -0` command.


```
@nutiu ➜ ~ py -0
Installed Pythons found by C:\WINDOWS\py.exe Launcher for Windows
 -3.10-64 *
 -3.7-64


@nutiu ➜ ~ py
Python 3.10.3 (tags/v3.10.3:a342a49, Mar 16 2022, 13:07:40) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
```


The default version has a star next to it. If we run a simple **py** command, we’ll get a prompt to Python 3.10. To change the default version all we need to do is to set the environment variable **PY\_PYTHON** to the desired version.


```
@nutiu ➜ ~ $env:PY_PYTHON = "3.7"
@nutiu ➜ ~ py -0
Installed Pythons found by C:\WINDOWS\py.exe Launcher for Windows
 -3.10-64
 -3.7-64 *
@nutiu ➜ ~ py
Python 3.7.0 (v3.7.0:1bf9cc5093, Jun 27 2018, 04:59:51) [MSC v.1914 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
```


## Using the Python command


If you prefer running Python using the full command then you’ll get the Python version which has higher precedence in your path, for example if I run `python` on my machine I will get:


```
@nutiu ➜ ~ python
Python 3.10.3 (tags/v3.10.3:a342a49, Mar 16 2022, 13:07:40) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
```


We can change the order by going to: **My PC** -> **Advanced System Settings** -> **Environment Variables**


Select path from User variables and click **Edit…**


![](/wp-content/uploads/2022/04/image-1.png?w=689)
Python 3.10 has higher precedence in path because it is above Python 3.7. If we want to change the order, we need to select the folders referencing Python37 and click **Move Up** until they are above Python 3.10


![](/wp-content/uploads/2022/04/image-2.png?w=530)
Restarting your terminal and running `python` again should run your desired Python version.


Thanks for reading! 🍻