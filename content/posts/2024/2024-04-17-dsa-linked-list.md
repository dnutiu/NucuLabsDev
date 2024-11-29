---
title: "The Linked List"
author: [ "Denis Nutiu" ]
date: '2024-04-17'
categories:
  - "Kotlin"
  - "Data Structures & Algorithms"
permalink: /2024/04/17/the-linked-list
image: /hugo-content/2024-04/the-linked-list.jpg
tags:
  - linked list
  - data structures
  - algorithms
  - kotlin
layout: post
---

# Introduction

A linked list is a fundamental data structure which consists of **Nodes** that are connected to each other.

Other variations are:

- Double linked list
- Circular linked list (circular buffer)

# The Singly Linked List

To visualize the data structure, if you want to store two integers 10 and 20 you will have a 2 node linked list that
will have: [Node 1, Value 10] -> [Node 2, Value: 20] -> [null]

![Linked List](/hugo-content/2024-04/the-linked-list.jpg)

Node 1 points to Node 2 and Node 2 points to null.

The Linked List **data structure** permits the storage of data in an efficient manner.

Unlike arrays, the linked list data structure doesn't pre-allocate space for the data, it grows and shrinks at runtime
when needed.

The Linked List has many applications, but here are a few:

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

The space and time complexity of the linked list operations depends on the implementation.

A basic implementation which stores the **head** and tail **nodes** has the following complexity:

**Space Complexity** is **O(N)**, the list grows or shrinks linearly depending on how many elements are added at
runtime.

The **Time Complexity** is differing for each operation:

- **Append element to the end** of the list takes **O(1)** time.
- **Insert at position** takes **O(N)** time.
- **Delete** at position takes **O(N)** time.
- **Get** first and last elements will take **O(1)** time.
- **Get** at position N will take **O(N)** time.

The operations that take **O(N)** time takes this much because you have to traverse the list's for at least N nodes in
order to perform it successfully. On the other hand, operations that take **O(1)** time do not require any traversals
because the list holds pointers to the **head** (first Node) and **tail** (last Node).

# Implementation in Kotlin

*If you would like, feel free to stop reading the article here and start implementing a generic linked list in your
favorite programming language. For me, it was a fun learning experience and I'll hope it will be the same for you.*

You'll find the Kotlin implementation for the Linked List on the GitHub
repo: [Implementation](https://github.com/dnutiu/dsa/blob/master/src/main/kotlin/data_structures/linked_list/LinkedList.kt) | [Tests](https://github.com/dnutiu/dsa/blob/master/src/test/kotlin/data_structures/linked_list/LinkedListTest.kt).

Let's review some:

## Append an element at the end of the list

Append appends an element's value at the end of the list.
Since the method can access the head and tail of the list, the implementation is fairly straightforward.

If the list contains at least one element, use the tail to append the element then update the tail.
If the list is empty, then add the new node to the **head** and **tail**.

```kotlin
    fun append(value: T) {
        val newNode = Node(value, null)
        if (head == null) {
            head = newNode
            tail = newNode
        } else {
            tail!!.next = newNode
            tail = newNode
        }
        size += 1
    }
```

## Get an element's value from the list

To get an element from the list at position N the list has to be traversed until the position is reached.

A possible optimization here can be whether the traversal should start from the head or tail of the list.

```kotlin
    fun get(position: Int): T? {
        var currentPosition = 0
        var currentNode = head
        while (currentPosition <= position && currentNode != null) {
            if (currentPosition == position) {
                return currentNode.value
            }
            currentNode = currentNode.next
            currentPosition += 1
        }
        return null
    }
```

# Iterator Pattern

To iterate though the list in a Kotlin friendly fashion with `linkedList.forEach {}` the iterator pattern needs to be
implemented.

Since the iterable is the `Node<T>` the list needs to implement the `Iterable<Node<T>>` interface, then implement the
iterator function:

```kotlin
    override fun iterator(): Iterator<Node<T>> {
        return NodeIterator(this)
    }
```

The NodeIterator is an inner class that implements the `Iterator<Node<T>>` interface and provides the
iteration functionality.

```kotlin
    class NodeIterator<T>(linkedList: LinkedList<T>) : Iterator<Node<T>> {
        private var currentNode: Node<T>? = linkedList.head

        override fun hasNext(): Boolean {
            return currentNode != null
        }

        override fun next(): Node<T> {
            val node = currentNode!!
            currentNode = currentNode?.next
            return node
        }
    }
```

## listOf - An elegant constructor

Constructs a linked list from a variable number of arguments.

The `listOf` method is implemented with the help of an extension method:

```kotlin
fun <T> LinkedList.Companion.listOf(vararg items: T): LinkedList<T> {
    val linkedList = LinkedList<T>()
    items.forEach {
        linkedList.append(it)
    }
    return linkedList
}
```

It provides the user of the Linked List API an elegant way to instantiate it: `LinkedList.listOf(1, 2)`.

## toArray - Transforms the Linked List ist into an array

The `toArray` is tricky to implement as an instance method.
Since generic types are erased at runtime, the method doesn't know what type `T` is.

By making this a generic extension method and defining a new reified type `E` the type of the generic
method can be omitted when calling it. The compiler can infer it automatically.


For example, calling `toArray()` on a `LinkedList<String>`the type parameter `E` will be `linkedList.toArray<String>()`.

```kotlin
inline fun <reified E> LinkedList<E>.toArray(): Array<E?> {
    val newArray = arrayOfNulls<E>(this.size())
    this.forEachIndexed { i, node ->
        newArray[i] = node.value
    }
    return newArray
}
```