---
author: "Denis Nu\u021Biu"
categories:
- Design Pattern
- Programming
date: '2020-12-28T12:28:10+00:00'
guid: http://nuculabs.dev/?p=1174
id: 1174
layout: post
permalink: /2020/12/28/method-injection-and-property-injection-design-patterns/
tags:
- Design Patterns
- method injection
- property injection
- Python
title: Method Injection and Property Injection Design Patterns
---
Hello,


In this article we’re going to explore the Method Injection and Property Injection design patterns.


To demonstrate the patterns I’m going to add a new interface named Encoder to the printer.py file and a concrete implementation for two encoders: Rot13Encoder and NullEncoder.


```
class Encoder(metaclass=abc.ABCMeta):
    def encode(self, message: Message) -> Message:
        raise NotImplementedError("encode must be implemented!")


class Rot13Encoder(metaclass=abc.ABCMeta):
    def encode(self, message: Message) -> Message:
        return Message(codecs.encode(str(message), 'rot_13'))


class NullEncoder(metaclass=abc.ABCMeta):
    def encode(self, message: Message) -> Message:
        return message
```


The Encoder will be used by the printer in order to encode the messages before printing them.


## Method Injection


The method injection pattern is used as an alternative to the constructor injection when the dependency is optional or is only used in one spot, so it wouldn’t make sense to inject it in the constructor.


My console printer would look like this If I’d use this pattern:


```
class ConsolePrinter(Printer):
    def __init__(self, prefix: str):
        self._prefix = prefix

    def print(self, message: Message, encoder: Encoder):
        print(self._prefix, encoder.encode(message))
```


When the application.py would call Printer.print it would pass the Encoder as a dependency.


## Property Injection


The property injection patter is mostly used in libraries, applications should avoid it. To use the property injection pattern I would have to modify the ConsolePrinter class like so:


```
class ConsolePrinter(Printer):
    def __init__(self, prefix: str):
        self._prefix = prefix
        self.encoder = NullEncoder()

    def print(self, message: Message):
        print(self._prefix, self.encoder.encode(message))
```


I have a property called encoder which by default acts as a NullEncoder, if for some reason the user of the library needs to change it, it can do so by injecting the needed dependency in the property.


The code for the [Property Injection ](https://github.com/dnutiu/NucuLabs-code/tree/master/python-property-injection-design-pattern)and[ Method Injection ](https://github.com/dnutiu/NucuLabs-code/tree/master/python-method-injection-design-pattern)patterns is on my Github! 🙂


Thanks for reading!