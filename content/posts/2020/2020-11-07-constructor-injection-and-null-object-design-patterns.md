---
author: "Denis Nu\u021Biu"
categories:
- Design Pattern
- Programming
- Tutorial
date: '2020-11-07T17:46:17+00:00'
guid: http://nuculabs.dev/?p=1159
id: 1159
image: /wp-content/uploads/2020/11/coffee-2306471_1280.jpg
layout: post
permalink: /2020/11/07/constructor-injection-and-null-object-design-patterns/
tags:
- Design Patterns
- Python
title: Constructor Injection and Null Object Design Patterns
---
The Constructor Injection design pattern is a pattern that helps you declare all the required dependencies of a class in it’s constructor.


This is useful because it helps you decouple the code, you can specify an interface instead of a concrete type, remember, *program to an interface*.


Also, in the constructor it is easier to guard against null objects. The calling code doesn’t have to worry about null exceptions every time it uses a dependency.


Avoid providing defaults when using this pattern, as this will couple the code with a concrete type. When a dependency is not needed, use the Null Object pattern.


We’re going to pickup from the last article and show you how you can modify the application to use the **constructor injection** and **null object** design patterns.


The class graph will look like this:


![](/wp-content/uploads/2020/11/constructor-injector-design-pattern1.png?w=541)
In order to demonstrate this pattern I will introduce a new class **MessageTranslator**.


```
class MessageTranslator:
    def __init__(self, translator: Translator, printer: Printer):
        if not translator:
            raise ValueError("Translator cannot be None.")
        if not printer:
            raise ValueError("Printer cannot be None.")

        self._translator = translator
        self._printer = printer

    def translate(self, message):
        return self._translator.translate(message)

    def print(self, message):
        self._printer.print(message)
```


And modify the Application code to use it:


```
class Application:
    def __init__(self):
        self._input_listener: InputListener = ConsoleInputListener("< ")

    def start(self):
        print("starting application.")
        message_translator = MessageTranslator(RomanianTranslator(), ConsolePrinter(">"))
        while True:
            user_in = Message(self._input_listener.get_input())
            if str(user_in) == "exit":
                exit(0)

            translated_message = message_translator.translate(user_in)
            message_translator.print(translated_message)
```


That’s it! You’ve used the constructor injection pattern.


Now, if don’t want to print the translated message into the console we can’t just pass a null Printer, that would raise an exception.


We need use the null object pattern to implement a **Printer** that does nothing.


```
class VoidPrinter(Printer):
    def print(self, message):
        pass
```


If we modify the Application code to use our VoidPrinter, the output would be:


```
starting application.
 < hello Dev
 < 
```


Thanks for reading! As always, the full code can be found on my [Github](https://github.com/dnutiu/NucuLabs-code/tree/master/python-constructor-injection-design-pattern).