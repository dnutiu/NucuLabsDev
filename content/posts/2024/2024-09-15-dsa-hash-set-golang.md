---
title: "The Hash Set"
author: [ "Denis Nutiu" ]
date: '2024-09-15'
categories:
  - "Golang"
  - "Data Structures & Algorithms"
permalink: /2024/09/15/the-hash-set
image: /hugo-content/2024-09/the-hash-set.png
tags:
  - set
  - hash set
  - data structures
  - algorithms
  - golang
layout: post
published: true
---

# Introduction

A hash set is a data structure that allows storing elements inside a set using a hash function.

The set is a data structure that offers efficient access to its elements and does not allow duplicates. The uniqueness
of an element in the hash set is determined by the hash function, in this implementation hash
collisions are not handled. If the hash of "a" is 123 and the hash of "b" is 123 as well then we consider "a" and "b"
to have a hash collision and if "a" is already present in the set adding "b" has no effect.

# Space and Time Complexity

The space and time complexity depends on the underlying data structure used, in this case a map is used.

The time complexity of Golang's maps is on average O(1) for all operations and the space complexity is O(n).

# Operations

The following operations have been implemented:

## Add

Add adds an element to the set, it doesn't handle collisions.

```golang
func (s *MySet[T, H]) Add(element T) {
	// Hash element
	hash := element.Hash()

	// Save
	_, ok := s.storage[hash]
	if !ok {
		s.storage[hash] = element
	}
}
```

## AddAll

AddAll adds all the elements to the set. It's a convenience method that allows only one method call.

```golang
// AddAll adds all the elements to the set.
func (s *MyHashSet[T, H]) AddAll(elements ...T) {
	for _, element := range elements {
		s.Add(element)
	}
}
```

## Contains

```golang
// Contains checks if the hash set contains the element T.
// Returns true if the element is part of the set, false otherwise.
func (s *MyHashSet[T, H]) Contains(element T) bool {
	// Hash element
	hash := element.Hash()
	_, ok := s.storage[hash]
	return ok
}
```

## Delete

```golang
// Delete deletes an element from the set.
// Returns true if the element was deleted, false otherwise.
func (s *MyHashSet[T, H]) Delete(element T) bool {
	// Hash element
	hash := element.Hash()
	_, ok := s.storage[hash]
	if ok {
		delete(s.storage, hash)
		return true
	}
	return false
}

```

## Union

```golang
// Union creates a union of two sets
func (s *MyHashSet[T, H]) Union(other *MyHashSet[T, H]) {
	for _, v := range other.storage {
		s.Add(v)
	}
}
```
## Sub

```golang
// Sub creates a difference of two sets
func (s *MyHashSet[T, H]) Sub(other *MyHashSet[T, H]) {
	for _, v := range other.storage {
		s.Delete(v)
	}
}
```

# Additional Notes

To implement the Set in a generic manner a custom interface has been defined to represent a hash:

```golang
type MyHash interface {
	~string | ~int | ~uint | ~int64 | ~uint64 | ~int32 | ~uint32 | ~int16 | ~uint16 | ~int8 | ~uint8
}
```

The ~ (tilda) defines a type constraint which translates to ~string - all subtypes of string and string.

It allows you to write code like:

```golang
type String string

func (a String) Hash() string {
	return string(a)
}

func TestMyHashSet_Constraint(t *testing.T) {
	// Setup
	newSet := NewSet[String, string]()

	// Test
	newSet.AddAll("type", "constraints", "rock")

	// Then
	assert.True(t, newSet.Contains("type"))
	assert.True(t, newSet.Contains("constraints"))
	assert.True(t, newSet.Contains("rock"))
}
```
The hash set structure is defined as:

```golang
// MyHashSet is a hash set implementation.
type MyHashSet[T Hasher[H], H MyHash] struct {
	storage map[H]T
}
```

The generic parameters are specified in the form `[T Hasher[H], H MyHash]` where T is the `Hasher[H]` and
H is a concrete type of MyHash. Hasher cannot be initialized without a concrete type.

When initializing the hash set you will need to specify the `String` 

You can browse the [full implementation](https://github.com/dnutiu/dsa-go/tree/master/hash_set) on my GitHub.