---
author: "Denis Nu\u021Biu"
categories:
- Design Pattern
- Programming
- Tutorial
date: '2020-11-06T20:41:33+00:00'
guid: http://nuculabs.dev/?p=1136
id: 1136
image: /wp-content/uploads/2020/11/coffee-2306471_1280.jpg
layout: post
permalink: /2020/11/06/composition-root-pattern-how-to-write-modular-software/
tags:
- Composition Root
- Design Patterns
- Python
title: 'Composition Root Pattern: How to Write Modular Software'
---
The composition root is a design pattern which helps you structure a software application by implementing a class that builds all the other classes.


In this example we will examine this pattern in Python.


Here’s the object graph of the classes that we’re going to implement:


![](/wp-content/uploads/2020/11/composition_root_object_graph2.png?w=1024)
I have designed a sample application that we’re going to use. It contains three components: ConsoleInputListener, ConsolePrinter and RomanianTranslator and a value object class: Message.


The classes are described as follows:


- Application: The composition root, glues all the classes together.
- ConsoleInputListener: Component, it reads string from the standard input.
- ConsolePrinter: Component, it prints to the standard output.
- RomanianTranslator: Component, it translates English words to Romanian.
- Message: Value object, it encapsulates the message string.


### *
Program to an interface not an implementation
*


Before implementing the Application component, I’m going to define the interfaces for the ConsoleInputListener, ConsolePrinter and RomanianTranslator. I’m going to call them InputListener, Printer and Translator for simplicity.


The reason I’m defining interfaces\* is because I want to be able to swap the objects that the Application class references. In Python variables don’t constrain me to any type, but if I’m going to implement other objects, I’d like to have a template so it will help me reduce the number of mistakes that I can make.


Python doesn’t have support for interfaces so I’m going to use abstract classes:


```
```
class Printer(metaclass=abc.ABCMeta):
    def print(self, message):
        raise NotImplementedError("print is not implemented")


class InputListener(metaclass=abc.ABCMeta):
    def get_input(self) -> str:
        raise NotImplementedError("get_input is not implemented!")


class Translator(metaclass=abc.ABCMeta):
    def translate(self, message: Message) -> Message:
        raise NotImplementedError("translate must be implemented!")
```
```
Every class that extends my abstract classes must implement it’s abstract methods:


```
<pre class="wp-block-code">```
class ConsolePrinter(Printer):
    def __init__(self, prefix: str):
        self._prefix = prefix

    def print(self, message: Message):
        print(self._prefix, message)

class ConsoleInputListener(InputListener):
    def __init__(self, prompt: str):
        self._prompt = prompt

    def get_input(self) -> str:
        return input(self._prompt)

class RomanianTranslator(Translator):
    def translate(self, message: Message) -> Message:
        words_map = {"hello": "salut"}
        message_words = str(message).split(" ")

        for index, word in enumerate(message_words):
            if word.lower() in words_map.keys():
                message_words[index] = words_map[word]

        return Message(" ".join(message_words))
```
```

The Message class, for the sake of completeness only holds a string.

```
```
class Message:
    def __init__(self, message):
        self._message = message

    def __str__(self):
        return self._message

```
```
And finally, the Application class will glue all the components together and instantiate them:

```
```
from input_listener import InputListener, ConsoleInputListener
from message import Message
from printer import Printer, ConsolePrinter
from translator import Translator, RomanianTranslator




class Application:
    def __init__(self):
        self._printer: Printer = ConsolePrinter(">")
        self._translator: Translator = RomanianTranslator()
        self._input_listener: InputListener = ConsoleInputListener("< ")


    def start(self):
        print("starting application.")
        while True:
            user_in = Message(self._input_listener.get_input())
            if str(user_in) == "exit":
                exit(0)


            self._printer.print(self._translator.translate(user_in))


```
```


The main method will just run the Application:


```
```
from application import Application




def main():
    app = Application()
    app.start()




if __name__ == '__main__':
    main()


```
```
Running the application would output:


```
<pre class="wp-block-preformatted"> starting application.
 < hello Denis!
 > salut Denis! 
```


- - - - - -


Now, most real world applications aren’t this simple, the reason we went through all this code in order to implement a simple *hello world* is the following: Imagine that you have 10 translators: English, French, German… and two Printers: Console and File.


You could modify the application code to take in two parameters: translator and printer use the arguments to instantiate the correct translator and printer without needing to change the other classes. You can add as many printers, translators and input listeners as you wish. That’s a huge benefit.


If you were to inline all the code in a single class, adding more translations and more printing options would have been very painful and frustrating.


I hope my article gave you some insight into the Composition Root pattern, if I made any mistake please feel free to correct me. 🙂


The full code is on my [Github](https://github.com/dnutiu/NucuLabs-code/tree/master/python-component-root-design-pattern).


Thanks for reading!