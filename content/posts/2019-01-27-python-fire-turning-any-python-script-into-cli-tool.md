---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Python
date: '2019-01-27T12:28:53+00:00'
guid: https://nuculabs.wordpress.com/?p=329
id: 329
layout: post
permalink: /2019/01/27/python-fire-turning-any-python-script-into-cli-tool/
tags:
- cmd
- library
title: Python Fire &#8211; Turning any Python script into a command line tool
---
It’s been a while since I wrote a post and I’m also going to make this one a short one.


[Python Fire](https://github.com/google/python-fire) is a pretty cool Python library from Google that lets you turn any Python script into a command line tool, this means that you don’t have to write any logic for handling command line parameters and options, but, if you script has sophisticated use cases then you’ll be probably be better by using [argparse](https://docs.python.org/3/library/argparse.html) from the standard library.


You can install Python fire from pip by running these commands:


```
pip install ipython
pip install fire
```


Then, to turn your script into a command line tool, all you need to do is import fire and and call the Fire method by passing it a class. Fire parses the class and generates logic for handling command line arguments for each method the class has. For example:


```
class LzwCommandLineAdapter:
    """
    This will be used with Google Fire for easy usage,
    so we can avoid argparse/optparse.
    """
    @staticmethod
    def encode(filename, out="encoded.txt"):
        """
        Encodes the text from the give filename.
        """
        pass
    @staticmethod
    def decode(filename, out="decoded.txt"):
        """
        Decodes the text from the given filename.
        """
        pass
 
if __name__ == "__main__":
    import fire
    fire.Fire(LzwCommandLineAdapter)
```


When we run the script, we get the following result:


```
➜  Desktop python lzw.py
 Type:      LzwCommandLineAdapter
 File:      ~/Desktop/lzw.py
 Docstring: This will be used with Google Fire for easy usage,
 so we can avoid argparse/optparse.
 Usage:     lzw.py
            lzw.py decode
            lzw.py encode
 ➜  Desktop python lzw.py decode
 Fire trace:
 Initial component
 Instantiated class "LzwCommandLineAdapter" (lzw.py:248)
 Accessed property "decode" (lzw.py:264)
 ('The function received no value for the required argument:', 'filename') 
 Type:        function
 String form: 
 File:        ~/Desktop/lzw.py
 Line:        264
 Docstring:   Decodes the text from the given filename.
 Usage:       lzw.py decode FILENAME [OUT]
              lzw.py decode --filename FILENAME [--out OUT]
```


This is a pretty nice solution when you’re writing lots of Python scripts to experiment with things. Thank you for reading!


**Further reading**


- 