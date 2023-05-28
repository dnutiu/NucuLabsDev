---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2021-07-29T21:57:46+00:00'
guid: https://nuculabs.dev/?p=1524
id: 1524
layout: post
permalink: /2021/07/29/testing-python-projects-with-tox/
tags:
- CI
- docker
- Python
- testing
- tox
- unit testing
title: Testing Python projects with Tox
---
Hi 👋


In this article I will show you how to test your Python projects with [Tox](https://tox.readthedocs.io/en/latest/).


## Introduction


Tox is a tool for automating testing in Python, their vision is to standardize the testing process. It can be used to easily test your project using multiple Python interpreters and run various commands.


## Getting Started


To get started all you need to add to your project is a tox.ini file. To simplify running the tests we will make use of the following Dockerfile, which contains Python interpreters for 3.6 and 3.7


```
FROM ubuntu:20.04


RUN apt update && apt install -y software-properties-common \
               && add-apt-repository ppa:deadsnakes/ppa \
               && apt install -y python3.6 && apt install -y python3.7 \
               && apt install -y python3-pip && pip3 install tox


VOLUME /code


WORKDIR /code
ENTRYPOINT tox
```


A tox.ini file which tests using python 3.6 and python 3.7 looks like this:


```
# content of: tox.ini , put in same dir as setup.py
[tox]
skip_missing_interpreters = True
envlist = py36,py37


[testenv]
# install pytest in the virtualenv where commands will be executed
deps =
    pytest==6.2.1
    pytest-cov==2.11.1
    responses==0.13.3
commands =
    # NOTE: you can run any command line tool here – not just tests
    pytest


[testenv:bamboo]
commands =
  pytest —junitxml=results.xml \
    —cov=your-module—cov-config=tox.ini —cov-report=xml
    coverage2clover -i coverage.xml -o clover.xml
deps =
    {[testenv]deps}
    coverage2clover
```


We have two environments: testenv and testenv:bamboo, the later one being used for coverage reporting in Bamboo using clover. To run Tox with a specific environment you’d type **tox -e bamboo**.


To run the test via the Dockerfile, first you’d build the docker container using: **docker build . -f Dockerfile -t tox**


Then, you’d run the container with **docker run -v “$(pwd)”:”/code” tox -e bamboo** to test with the Bamboo environment or just  **docker run -v “$(pwd)”:”/code” tox** for the default env.


## Practical Example


Here’s an example that you can use to follow along. We have the following files:


```
@denis ➜ tox_article ls
__pycache__  tests.py  tox.ini
```


```
# @denis ➜ tox_article cat tests.py
import unittest


class TestStringMethods(unittest.TestCase):


    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')


    def test_isupper(self):
        self.assertTrue('FOO'.isupper())
        self.assertFalse('Foo'.isupper())


    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):
            s.split(2)


if __name__ == '__main__':
    unittest.main()%
```


```
# @denis ➜ tox_article cat tox.ini
[tox]
skip_missing_interpreters = True
envlist = py36,py37
skipsdist = True


[testenv]
commands =
    python -m unittest%
```


Running Tox in our docker image will yield the following output:


```
@denis ➜ tox_article docker run -v "$(pwd)":"/code" tox
py36 create: /code/.tox/py36
py36 run-test-pre: PYTHONHASHSEED='520882151'
py36 run-test: commands[0] | python -m unittest
...
----------------------------------------------------------------------
Ran 3 tests in 0.000s


OK
py37 create: /code/.tox/py37
py37 run-test-pre: PYTHONHASHSEED='520882151'
py37 run-test: commands[0] | python -m unittest
...
----------------------------------------------------------------------
Ran 3 tests in 0.000s


OK
___________________________________ summary ____________________________________
  py36: commands succeeded
  py37: commands succeeded
  congratulations :)
```


The same tests are run twice, first with Python 3.6 and then with Python 3.7.


Thanks for reading and happy testing! 🔧