---
title: "My experience with BambuLab A1 and 3D printing"
author: [ "Denis Nutiu" ]
date: '2024-02-04'
categories:
  - "Review"
  - "Life"
  - "Engineering"
permalink: /2024/02/04/bambulab-a1-3d-printer-experience
image: /hugo-content/2024-02/nuculabs-dev-box.JPG
tags:
  - 3d
  - printer
  - freecad
  - cad
  - engineering
layout: post
published: false
---

# Introduction

A linked list is a fundamental data structure which consists of **Nodes** that are connected to each other.

There are multiple variations and possible implementations of a linked list. This article will only cover the **single linked** list and it's backed by a [Kotlin](https://github.com/dnutiu/dsa/blob/master/src/main/kotlin/data_structures/linked_list/LinkedList.kt) implementation along with [unit-tests](https://github.com/dnutiu/dsa/blob/master/src/test/kotlin/data_structures/linked_list/LinkedListTest.kt).

Other variations that come to my mind right now are:
- Double linked list
- Circular linked list (circular buffer)
# What is a Linked List

The Linked List **data structure** allows permits the storage of data in an efficient manner. 

To visualize the data structure, if you want to store two integers 10 and 20 you will have a 2 node linked list that will have: [Node 1, Value 10] -> [Node 2, Value: 20] -> [null]
![[linked-list 1.jpg]]
Node 1 points to Node 2 and Node 2 points to null.

Unlike arrays, the linked list data structure doesn't pre-allocate space for the data, it grows and shrinks at runtime when needed.

The Linked List has many applications but here are a few:
- Implementing undo-redo in a text editor.
- Representing a musical playlist.
- Managing a cache where entries are added and removed based on usage.
* Storing data in a system where memory is limited such as embedded systems.
* Buffering.

Depending of the implementation it can support operations such as:
- insert element at position
- append element
- delete last element
- delete element
- get first element
- get last element
- get element at position
- reverse list

# Space and Time Complexity

The space and time complexity of the linked list operations depends on the implementation but considering a basic implementation which stores the **head** and tail **nodes** in variables we can say that the:

**Space Complexity** is **O(N)**, the list grows or shrinks linearly depending on how many elements are added at runtime.

The **Time Complexity** is differs for each operation:
- **Append element to the end** of the list takes **O(1)** time.
- **Insert at position** takes **O(N)** time.
- **Delete** at position takes **O(N)** time.
- **Get** first and last elements will take **O(1)** time.
- **Get** at position N will take **O(N)** time.

The operations that take **O(N)** time takes this much because you have to traverse the list's for at least N nodes in order to perform it successfully. On the other hand, operations that take **O(1)** time do not require any traversals because the list holds pointers to the **head** (first Node) and **tail** (last Node).

# Implementation in Kotlin

*If you would like, feel free to stop reading the article here and start implementing a generic linked list in your favorite programming language. For me, it was a fun learning experience and I'll hope it will be the same for you.*

Here you'll find my Kotlin implementation for the Linked List: [Implementation](https://github.com/dnutiu/dsa/blob/master/src/main/kotlin/data_structures/linked_list/LinkedList.kt) | [Tests](https://github.com/dnutiu/dsa/blob/master/src/test/kotlin/data_structures/linked_list/LinkedListTest.kt).



- Implementation in Kotlin: 
* append, get, delete, iterator, listOf

--
*Note*: If you are interested in this kind of topic, I found a GitHub organization called [TheAlgorithms](https://github.com/TheAlgorithms) which provides educational implementations for a bunch of algorithms in various programming languages.

- Kotlin - https://github.com/TheAlgorithms/Kotlin
- Java - https://github.com/TheAlgorithms/Java
- Python - https://github.com/TheAlgorithms/Python
- Go - https://github.com/TheAlgorithms/Go