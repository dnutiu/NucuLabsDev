---
author: ["Denis Nu\u021Biu"]
categories:
- Programming
- Tutorial
date: '2023-04-13T20:43:49+00:00'
layout: post
permalink: /2023/04/23/getting-started-with-pdoc3-for-python/
tags:
- pdoc
- documentation
- tutorial
- python
title: "Getting Started with pdoc3 for Python"
---

Hello everyone! 👋

In this article we’ll talk about documentation and how to document a Python project with [pdoc](https://pdoc.dev/).

![pdoc-feature](/hugo-content/2023-05/pdoc-feature.webp)

Documentation is an essential part of writing high-quality Python code. Not only does it make your code easier to understand, but it also makes it more maintainable and reusable. In this post, we'll explore how to use pdoc to generate documentation for your Python code.

pdoc is a powerful tool that can automatically generate documentation for your Python modules and classes based on their docstrings. Docstrings are special strings that appear at the beginning of a module, function, or class definition, and provide a description of the object's purpose, parameters, return values, and other relevant information.

Here's an example of a docstring for a simple Python function:

```python
def greet(name: str) -> str:
    """
    Return a personalized greeting message.

    Parameters
    ----------
    name : str
        The name of the person to greet.

    Returns
    -------
    str
        A greeting message addressed to the specified person.
    """
    return f"Hello, {name}!"
```

In the above example, the docstring for the greet function includes a brief description of its purpose, as well as information about its parameters and return value.

To generate documentation for this function using pdoc, you would first need to install pdoc using pip:

```bash
pip install pdoc
```

Once installed, you can generate HTML documentation for your Python code using the pdoc command:

```bash
pdoc --html mymodule.py
```

This will generate a set of HTML files in a directory named mymodule.html, which you can then view in a web browser.

By default, pdoc will use the Google-style docstring format, which uses triple quotes to enclose docstrings and includes sections for Args, Returns, and Raises. However, you can also customize the format of your docstrings to suit your needs.

For example, here's a different way to document the greet function using a more concise docstring format:

```python
def greet(name: str) -> str:
    """
    Return a personalized greeting message.

    name: str
        The name of the person to greet.

    Returns:
        A greeting message addressed to the specified person.
    """
    return f"Hello, {name}!"
```

In this format, the function's parameters and return value are described using a simple colon-separated syntax.

Finally, it's worth noting that pdoc can also generate documentation for classes, as well as other types of Python objects like modules and packages. Here's an example of a docstring for a simple Python class:

```python
class Person:
"""
A simple class representing a person.

    Attributes
    ----------
    name : str
        The person's name.
    age : int
        The person's age.
    """

    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age
```

In the above example, the docstring for the Person class includes a brief description of its purpose, as well as information about its attributes.

A more complete example would look like this

![pdoc-code](/hugo-content/2023-05/pdoc-code.webp)

And the code:

```python
def run_binary(binary_path: str) -> None:
    """
    Run a binary.

    Parameters
    ----------
    binary_path : str
        The directory path that contains the binary name.

    Raises
    ------
    FileNotFoundError
        If the binary file is not found.
    RuntimeError
        If the binary returns a non-zero exit code.

    Notes
    -----
    This function assumes that the binary file is executable.

    Examples
    --------
    >>> run_binary('/usr/local/bin/')
    """
    # Function code here
```

In the above example, the docstring for the run_binary function includes the following fields:

- Parameters: The parameters of the function, including their types and descriptions.
- Returns: The return value of the function, including its type and description (in this case, None).
- Raises: The exceptions that the function may raise, including their names and descriptions.
- Notes: Additional notes about the function that may be useful to the reader.
- Examples: Examples of how to use the function, including expected inputs and outputs.

The generated documentation will look like this:

![pdoc-example](/hugo-content/2023-05/pdoc-example.webp)

In conclusion, documenting your Python code is an important part of the software development process, and pdoc can make it easier than ever to generate high-quality documentation for your modules, classes, and functions.

Thanks for reading! 📚