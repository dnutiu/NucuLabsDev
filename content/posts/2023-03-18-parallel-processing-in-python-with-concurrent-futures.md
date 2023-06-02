---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Tutorial
date: '2023-03-18T20:14:31+00:00'
guid: https://nuculabs.dev/?p=2618
id: 2618
layout: post
permalink: /2023/03/18/parallel-processing-in-python-with-concurrent-futures/
tags:
- parallel
- pickle
- process
- ProcessExecutor
- ProcessPoolExecutor
- Python
- serializing
title: Parallel Processing in Python with concurrent.futures
---
Hello 👋,


In this short article I want to talk about parallel processing in Python.


# Introduction


Sometimes you will need to process certain things in parallel. If you’re using Python you may know about the global interpreter lock abbreviated **GIL** for short. The GIL is a lock that allows only a single thread to control the Python Interpreter at a time (per process), that means that your multi-threaded Python program will have only a single thread executing code at the same time.


To overcome the GIL problem, I highly recommend the [concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html) module along with the **[ProcessPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#processpoolexecutor)** which is available since Python 3.2.


The **ProcessPoolExecutor** comes with some limitations, you can only execute and return objects that can be [pickled](https://docs.python.org/3/library/pickle.html). The pickle module is used for serializing and deserializing Python objects.


# Computing Sums 


To demonstrate the use of the ProcessPoolExecutor I wrote a simple program for counting the sum of an array with 1\_000\_000\_000 elemets. On my machine the program executes in 20713ms.


```
from time import time


def main():
    elements = 1_000_000_000
    arr = [i for i in range(1, elements)]

    start = time()
    print(sum(arr))
    end = time() - start
    print("Duration", end * 1000, "ms.")


if __name__ == '__main__':
    main()
```


To speed up the program we can execute the code in parallel in multiple processes, instead of computing the sum in a single step we can split it in 100 steps and use the ProcessPoolExecutor to execute the compute sum for each step.


By running the following code:


```
from concurrent.futures.process import ProcessPoolExecutor
from time import time


def compute_sum(start, stop):
    arr = [i for i in range(start, stop)]
    return sum(arr)


def main():
    start = time()
    elements = 1_000_000_000

    with ProcessPoolExecutor(max_workers=20) as executor:
        # Steps will be a list from [0, 10000000, 20000000, ..., 990000000, 1000000000]
        steps = [i for i in range(0, elements + 1, elements // 100)]
        # Results will store our futures
        results = []
        for step, i in enumerate(range(2, len(steps)+1)):
            print("Submitting", step)
            # step is [0, 10000000] to [990000000, 1000000000]
            step = steps[i-2:i]
            # compute the sum of sub arrays, from 0 to 10000000; Returns a future
            future = executor.submit(compute_sum, step[0], step[1])
            # save the future
            results.append(future)

        # Retrieve the results and add up the sums.
        total_sum = 0
        for r in results:
            total_sum += r.result()
        print("Sum", total_sum)

    end = time() - start
    print("Duration", end * 1000, "ms.")


if __name__ == '__main__':
    main()
```


It will start 20 Python processes and each of them will get a task for computing the sum between a start range and stop range:


```
def compute_sum(start, stop):
    arr = [i for i in range(start, stop)]
    return sum(arr)
```


![](/wp-content/uploads/2023/03/image.png?w=1024)
Compared to the previous code, the one that uses the process pool executor runs only in ~7 seconds. That’s 3x time improvement!


# Conclusion


Running the program in parallel improved the running time by almost 3X on my machine.


Dividing a problem into sub-problems and solving each problem in a parallel manner is a good way to improve the performance of your programs.


In Python if we run the same code on multiple threads **only one thread executes at a time**, which defeats the purpose of running on multiple threads. To overcome this limitation we used the ProcessPoolExecutor from the concurrent.futures module to run the code in multiple Python processes and finally we combined the results.


Thanks for reading!