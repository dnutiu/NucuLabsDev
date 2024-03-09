---
title: "Cancellation Token Pattern in Python"
author: [ "Denis Nutiu" ]
date: '2024-03-09'
categories:
  - Python
  - Programming
permalink: /2024/03/09/cancellation-token-pattern-in-python
tags:
  - python
  - programming
  - design patterns
layout: post
published: true
---

Hello! 👋

The Cancellation Token Pattern article is a pattern inspired by
C#'s [CancellationToken](https://learn.microsoft.com/en-us/dotnet/api/system.threading.cancellationtoen?view=net-8.0)
struct and Golang's
[context](https://pkg.go.dev/context) package.

The main idea of the pattern is to **allow the user of an API** to cancel its operations, but in order
for this pattern to work, the API must be written with the cancellation token pattern in mind.

To use the pattern, you need a class that represents a cancellation token. Here's my simple version:

```python
class CancellationToken:
    def __init__(self):
        self._is_canceled = False

    def is_canceled(self):
        return self._is_canceled

    def cancel(self):
        self._is_canceled = True
```

Since `cancel()` is an **idempotent** operation the `CancellationToken` class can be safely used in a multithreaded
application.

The code of the API must be written with the `CancellationToken` in mind.
For example, consider the following piece of code is not written with the pattern in mind:

```python
while True:
    time.sleep(1)
    print("Hello world.")

```

It loops forever, sleeps for a second and prints the string "Hello World."
We can rewrite it using the cancellation token pattern like this:

```python
ct = CancellationToken()

while not ct.is_canceled():
    time.sleep(1)
    print('Hello World!')
```

The code now checks if the token is canceled, and if it is, it will stop running.

The user of the API has more control to when to cancel the execution of the operation/code.

Consider this example which cancels the token when it receives the SIGINT signal.
The signal SIGINT is sent by the operating system to the program when we press `CTRL+C` inside the terminal
or click the stop button in the IntelliJ IDE:

```python
import functools
import signal
import time


class CancellationToken:
    def __init__(self):
        self._is_canceled = False

    def is_canceled(self):
        return self._is_canceled

    def cancel(self):
        self._is_canceled = True


def main():
    # Setup cancellation token
    ct = CancellationToken()

    # Setup signal handling for SIGINT
    def signal_handler(cancellation_token: CancellationToken, sig, frame):
        print("Handling sigint")
        cancellation_token.cancel()

    signal.signal(signal.SIGINT, functools.partial(signal_handler, ct))

    # Block until signal is received
    while not ct.is_canceled():
        time.sleep(1)
        print('Hello World!')
    print("Done")


if __name__ == '__main__':
    main()
```

If an API has a more complex control flow like running multiple functions in parallel, we can pass the initial
cancellation token to each function and cancel all of them at once.

Golang has [WithTimeout](https://pkg.go.dev/context#WithTimeout) which creates a context that gets cancelled after
the specified timeout. To implement a similar feature in our CancellationToken we can subclass it in our
Python implementation and use [Timer](https://docs.python.org/3/library/threading.html#timer-objects) object to cancel
the token. Alternatively, we can add the timeout to the current time and call it `timeout_time`,
and when `is_canceled()` is called we can check if the current time is greater than or equal (`>=`)
than the `timeout_time`.

I hope you've enjoyed this article and **thank you for reading**! 🙏

*If you have any thoughts about this article, feel free to leave a comment.* 