---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Tutorial
date: '2022-05-12T13:04:22+00:00'
guid: https://nuculabs.dev/?p=2253
id: 2253
layout: post
permalink: /2022/05/12/go-pattern-sorting-a-slice-on-multiple-keys/
tags:
- go
- multisort
- slice
- sorting
title: 'Go Pattern: Sorting a slice on multiple keys'
---
Hi 👋


In this article I want to highlight a simple pattern for sorting a slice in Go on multiple keys.


Given the following structure, let’s say we want to sort it in ascending order after Version, Generation and Time.


```
type TheStruct struct {
	Generation int
	Time       int
	Version    int
}
```


The way we sort slices in Go is by using the [sort interface](https://pkg.go.dev/sort#Interface) or one of the [sort.Slice](https://pkg.go.dev/sort#Slice) functions. To sort the slice after the above criteria we’ll call slice.Sort with the following function.


```
	sort.Slice(structs, func(i, j int) bool {
		iv, jv := structs[i], structs[j]
		switch {
		case iv.Version != jv.Version:
			return iv.Version < jv.Version
		case iv.Generation != jv.Generation:
			return iv.Generation < jv.Generation
		default:
			return iv.Time < jv.Time
		}
	})
```


The slice will be sorted after the following fields: Version, Generation and Time. The trick is the switch statement and the case expression **case iv.Version != jv.Version** followed by the statement **return iv.Version < jv.Version**.


You can use this pattern whenever you want to sort slices over multiple fields in Go.


Thanks for reading! 🍻


## Source Code


```
package main

import (
	"fmt"
	"sort"
)

type TheStruct struct {
	Generation int
	Time       int
	Version    int
}

func main() {
	var structs = []TheStruct{
		{
			Generation: 1,
			Time:       150,
			Version:    0,
		},
		{
			Generation: 1,
			Time:       200,
			Version:    0,
		},
		{
			Generation: 1,
			Time:       200,
			Version:    2,
		},
		{
			Generation: 1,
			Time:       500,
			Version:    0,
		},
		{
			Generation: 1,
			Time:       100,
			Version:    0,
		},
		{
			Generation: 1,
			Time:       400,
			Version:    0,
		},
		{
			Generation: 2,
			Time:       400,
			Version:    0,
		},
		{
			Generation: 2,
			Time:       100,
			Version:    2,
		},
		{
			Generation: 1,
			Time:       300,
			Version:    0,
		},
	}

	fmt.Printf("%v\n", structs)

	sort.Slice(structs, func(i, j int) bool {
		iv, jv := structs[i], structs[j]
		switch {
		case iv.Version != jv.Version:
			return iv.Version < jv.Version
		case iv.Generation != jv.Generation:
			return iv.Generation < jv.Generation
		default:
			return iv.Time < jv.Time
		}
	})
	fmt.Printf("%v\n", structs)

}
```


**Output**


```
[{1 150 0} {1 200 0} {1 200 2} {1 500 0} {1 100 0} {1 400 0} {2 400 0} {2 100 2} {1 300 0}]
[{1 100 0} {1 150 0} {1 200 0} {1 300 0} {1 400 0} {1 500 0} {2 400 0} {1 200 2} {2 100 2}]
```


*Also, special thanks to RP.*💖