---
author: "Denis Nu\u021Biu"
categories:
- CTF Time
date: '2020-01-03T00:59:41+00:00'
guid: https://nuculabs.dev/?p=765
id: 765
layout: post
permalink: /2020/01/03/picoctf-2019-whats-the-difference/
tags:
- general knowledge
- picoCTF2019
title: 'PicoCTF 2019: whats-the-difference (Points 200)'
---
> Can you spot the difference? kitters cattos. They are also available at /problems/whats-the-difference… on the shell server


In order to easily solve this challenge, I’ve used xxd and cut to generate an ascii hexdump of the images:


```
➜  Downloads xxd kitters.jpg | cut -d ' ' -f 11 > kittens_text.txt


➜  Downloads xxd cattos.jpg  | cut -d ' ' -f 11 > cattos_text.txt
```
Since I wanted to do a per character diff I wrote the following Python script:


```
<pre class="wp-block-prismatic-blocks">```python
def main():
    new = open("cattos_text.txt")
    old = open("kittens_text.txt")
    old_file = old.readlines()
    new_file = new.readlines()
    print("Loaded lines", len(old_file), len(new_file))
    for line in zip(old_file, new_file):
        for number, old_char  in enumerate(line[0]):
            if old_char != line[1][number]:
                print(line[1][number], end="")

main()
```

And finally run the script:
```
➜  Downloads python diff.py
Loaded lines 212919 212919
picoCTF{th3yr3_a5_d1ff3r3nt_4s_bu773r_4nd_j311y_*}%
```
```