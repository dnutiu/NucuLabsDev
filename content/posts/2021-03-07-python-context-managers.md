---
author: "Denis Nu\u021Biu"
categories:
- Design Pattern
- Programming
- Tutorial
date: '2021-03-07T15:17:04+00:00'
guid: https://nuculabs.wordpress.com/?p=455
id: 455
layout: post
permalink: /2021/03/07/python-context-managers/
tags:
- context managers
- decorator
- Design Patterns
- Python
title: Context Managers and Cross Cutting concerns in Python
---
Hello,


In this short article I would like to talk about context managers. I personally consider that at the core they are just a form of decorators. If you don’t know what a decorator is check the[ Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern) Wikipedia article.


Decorators can be used to implement cross-cutting concerns. We have componentA and we need logging and security, we could write the logic for logging and security handling in componentA but some people consider component a should be componentA not componentAthatAlsoKnowsAboutSecurityAndOtherStuff. Since it’s not the component’s responsibility to authorize requests or log calls to a external logging service, we can wrap the componentA into a decorator that does just that.


A formal definition for cross-cutting concerns as taken from Wikipedia is the following:


> In aspect-oriented software development, **[cross-cutting concerns](https://en.wikipedia.org/wiki/Cross-cutting_concern)** are aspects of a program that affect other [concerns](https://en.wikipedia.org/wiki/Concern_(computer_science)). These concerns often cannot be cleanly decomposed from the rest of the system in both the design and implementation, and can result in either *scattering* (code duplication), *tangling* (significant dependencies between systems), or both.


And some examples of cross cutting concerns include:


- [Caching](https://en.wikipedia.org/wiki/Cache_(computing))
- [Data validation](https://en.wikipedia.org/wiki/Data_validation)
- [Error detection and correction](https://en.wikipedia.org/wiki/Error_detection_and_correction)
- [Information security](https://en.wikipedia.org/wiki/Information_security)
- [Logging](https://en.wikipedia.org/wiki/Log_file)
- [Memory management](https://en.wikipedia.org/wiki/Memory_management)
- [Monitoring](https://en.wikipedia.org/wiki/Observer_pattern)
- [Persistence](https://en.wikipedia.org/wiki/Persistence_(computer_science))


Since the context managers are sort of similar to decorators you can use them to implement cross cutting concerns. Let’s explore.


## Simple Example


In Python you can have two types of context managers: a function and a class. In order for the function to behave like a context manager it will need to be decorated with the **@contextmanager** decorator, and in order for a class behave like a context manager it needs to implement `__enter`\_\_ and `__exit__`.


Context managers can be called using the **with** statement. The following code snippet demonstrates two context managers:


- One that logs when the function is called and when it exits.
- One that intercepts the function arguments.


```
from contextlib import contextmanager


@contextmanager
def simple_context_manager(function):
    try:
        print("calling function")
        yield function
    finally:
        print("function call has ended")


class SimpleContextManager:
    def __init__(self, cb):
        self.cb = cb


    def _intercept(self, *args, **kwargs):
        print(f"calling with {args} {kwargs}")
        return print(*args, **kwargs)


    def __enter__(self):
        print("intercept start")
        return self._intercept


    def __exit__(self, exc_type, exc_val, exc_tb):
        print("intercept end")


def main():
    with simple_context_manager(print) as print_func:
        print_func("hi")


    with SimpleContextManager(print) as print_func:
        print_func("hi")
        print_func("hi", end="\n\n", sep=",")
        print_func("hi")


if __name__ == '__main__':
    main()
```


## Caching


What is caching? In short..


Caching is used to store the result of an expensive computation somewhere in memory or on a persistent storage device in order to optimize the program.


We have the **compute\_fibonacci** function, which is quite slow. A version that uses cache has been implementing in the **CachedComputeFibonacci** class. Notice how the code takes some time to output the result for the first call of `print(cached_compute_fibonacci(35))` statement but the second print in instant.


```
def compute_fibonacci(number):
    if number <= 1:
        return number
    return compute_fibonacci(number-1) + compute_fibonacci(number-2)




class CachedComputeFibonacci:
    def __init__(self):
        self._cache = {}


    def __call__(self, *args, **kwargs):
        number = args[0]
        if number in self._cache:
            return self._cache[number]
        result = compute_fibonacci(number)
        self._cache[number] = result
        return result


    def __enter__(self):
        return self


    def __exit__(self, exc_type, exc_val, exc_tb):
        pass


def main():
    # Non cached
    print(compute_fibonacci(10))


    # Cached
    with CachedComputeFibonacci() as cached_compute_fibonacci:
        print(cached_compute_fibonacci(35))
        print(cached_compute_fibonacci(35))






if __name__ == '__main__':
    main()
```


## Logging


Logging can be useful for debugging and auditing purposes.


```
def compute_fibonacci(number):
    if number <= 1:
        return number
    return compute_fibonacci(number-1) + compute_fibonacci(number-2)




class LoggedComputeFibonacci:
    def __init__(self):
        pass


    def __call__(self, *args, **kwargs):
        print(f"calling compute_fibonacci with args={args} kwargs={kwargs}")
        result = compute_fibonacci(args[0])
        print(f"compute_fibonacci={result}")
        return result


    def __enter__(self):
        return self


    def __exit__(self, exc_type, exc_val, exc_tb):
        pass


def main():
    # Logging
    with LoggedComputeFibonacci() as cached_compute_fibonacci:
        print(cached_compute_fibonacci(35))
        print(cached_compute_fibonacci(36))






if __name__ == '__main__':
    main()
```


## Error detection and correction


If you find yourself duplicating the same try/catch logic in multiple places of your code perhaps you can extract it into a context manager for handling errors:


```
from contextlib import contextmanager


@contextmanager
def my_error_handler():
    try:
        yield
    except ZeroDivisionError:
        print("abort abort")


def main():
    # error handling
    with my_error_handler():
        print("0 / 0 =", 0 / 0)






if __name__ == '__main__':
    main()
```


The code is definitely more cleaner this way, in my opinion.


Thanks for reading and I hope that you’ve learnt something!