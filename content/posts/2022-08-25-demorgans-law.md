---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2022-08-25T21:31:08+00:00'
guid: https://nuculabs.dev/?p=2314
id: 2314
image: /wp-content/uploads/2022/08/moon-landing-apollo-11-nasa-buzz-aldrin-41162.jpeg
layout: post
permalink: /2022/08/25/demorgans-law/
tags:
- demorgan&#039;s law
- hardware
title: DeMorgan&#8217;s Law
---
![](/wp-content/uploads/2022/08/image.png?w=459)
DeMorgan’s law is a simple law that I learned at UPT during one of my hardware classes. While it is useful in hardware it, it is also useful when writing programs.


If you have a condition like not (`A and B)`, you can rewrite it to `!A or !B`.


```
if __name__ == '__main__':
    a = True
    b = True

    if not (a and b):
        print("True")
    else:
        print("False")

    if not a or not b:
        print("True")
    else:
        print("False")
```