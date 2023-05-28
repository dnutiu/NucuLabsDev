---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2021-12-14T15:09:35+00:00'
guid: https://nuculabs.dev/?p=1885
id: 1885
image: /wp-content/uploads/2021/12/denis-cosmin-px8ct4avme0-unsplash.jpg
layout: post
permalink: /2021/12/14/pytest-fixtures-and-yield/
tags:
- generator
- pytest
- Python
- test fixture
- testing
- unit test
- yield
title: Pytest Fixtures and Yield
---
Hi 👋


In this short article I want to explain the use of the **yield** keyword in pytest fixtures.


### What is pytest?


[Pytest](https://docs.pytest.org/en/6.2.x/) is a complex python framework used for writing tests. It has lots of advanced features and it supports plugins. Many projects prefer pytest in addition to Python’s [unitttest](https://docs.python.org/3/library/unittest.html) library.


### What is a fixture?


A test fixture is a piece of code that **fixes** some common functionality that is required for writing the unit tests. This functionality can be


- a connection to the database
- a testing http server or client
- creation of a complex object


You can read more about [test fixtures](https://en.wikipedia.org/wiki/Test_fixture#Software) on Wikipedia.


### What does yield keyword do?


In Python, the yield keyword is used for writing generator functions, in pytest, the yield can be used to finalize (clean-up) after the fixture code is executed. Pytest’s documentation states the following.


> “Yield” fixtures `yield` instead of `return`. With these fixtures, we can run some code and pass an object back to the requesting fixture/test, just like with the other fixtures.
> 
> 
https://docs.pytest.org/en/6.2.x/fixture.html


An example code could be the following:


```
@pytest.fixture()
def my_object_fixture():
    print("1. fixture code.")
    yield MyObjectThatRequiresCleanUp()
    print("4. fixture code after yield.")
```


Running a sample test which utilizes the fixture will output:


```
collected 1 item                                                                                                                                                                       


tests\test_my_object.py 1. fixture code.
2. Initializing MyObjectThatRequiresCleanUp
3. test code.
.4. fixture code after yield.
```


Running the same test but now with the fixture my\_object\_fixture2, will output:


```
tests\test_my_object.py 1. fixture code.
2. Initializing MyObjectThatRequiresCleanUp
2.1 Entering
3. test code.
.3.1 Exiting
Clean exit
4. fixture code after yield.
```


I hope I could successfully ilustrate with these examples the order in which the testing and fixture code is run.


To run the tests, I’ve used `pytest --capture=tee-sys .` in the project root. The file contents are attached to the end of this article. The `--capture` parameter is used to capture and print the tests stdout. Pytest will only output stdout of failed tests and since our example test always passes this parameter was required.


## Conclusion


Pytest is a python testing framework that contains lots of features and scales well with large projects.


Test fixtures is a piece of code for fixing the test environment, for example a database connection or an object that requires a specific set of parameters when built. Instead of duplicating code, **fixing** the object’s creation into a fixture makes the tests easier to maintain and write.


yield is a python keyword and when it is used in conjunction with pytest fixtures it gives you a nice pythonic way of cleaning up the fixtures.


Thanks for reading! 📚


- - - - - -


### Contents of the Pytest fixtures placed in tests/\_\_init\_\_.py


```
import pytest


from my_object import MyObjectThatRequiresCleanUp




@pytest.fixture()
def my_object_fixture():
    print("1. fixture code.")
    yield MyObjectThatRequiresCleanUp()
    print("4. fixture code after yield.")




@pytest.fixture()
def my_object_fixture2():
    print("1. fixture code.")
    with MyObjectThatRequiresCleanUp() as obj:
        yield obj
    print("4. fixture code after yield.")
```


### Contents of my\_object.py


```
class MyObjectThatRequiresCleanUp:
    def __init__(self):
        print("2. Initializing MyObjectThatRequiresCleanUp")


    def __enter__(self):
        print("2.1 Entering")
        return self


    def __exit__(self, exc_type, exc_val, exc_tb):
        print("3.1 Exiting")
        if exc_type is None:
            print("Clean exit")
        else:
            print("Exception occurred: {}".format(exc_type))
```


### Contents of test\_my\_object.py placed in tests/test\_my\_object.py


```
from tests import my_object_fixture




def test_my_object(my_object_fixture):
    print("3. test code.")
```


PS: If you want to support this blog, I’ve made a[ Udemy course on FastAPI](https://www.udemy.com/course/build-a-movie-tracking-api-with-fastapi-and-python/?referralCode=47430716975684E04F85). Purchasing it through my referral link will give me 96% of the sale amount. Thanks!