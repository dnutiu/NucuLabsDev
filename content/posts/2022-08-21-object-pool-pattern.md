---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Tutorial
date: '2022-08-21T19:47:08+00:00'
guid: https://nuculabs.dev/?p=2271
id: 2271
layout: post
permalink: /2022/08/21/object-pool-pattern/
tags:
- Design Pattern
- Golang
- object pool
title: Object Pool Pattern
---
Hi 👋


In this article we’ll talk about the Object Pool pattern in Golang.


The Object Pool pattern is a design pattern used in situations when constructing objects is a costly operation, for example building an HTTPClient or DatabaseClient object can take some time.


By having a pool of resources, the resources are requested from the pool when needed and then returned when not needed so they can be reused later.


Programs can benefit from this pattern because once the object is constructed when you need it again, you’ll just grab an instance instead of constructing it again from scratch.


In Golang this pattern is easily implemented with [sync.Pool](https://pkg.go.dev/sync#Pool). Given a struct **Resource** struct, to implement an object pool we’ll need to pass the **NewResource** function to the pool.


To track how many active instances, we have of the object Resource, we use the **counter** variable.


### Resource


```
var logger = log.Default()
var counter = 0
 
type Resource struct {
    id string
}
 
func NewResource() *Resource {
    logger.Printf("NewResource called")
    counter += 1
    return &Resource{id: fmt.Sprintf("Resource-%d", counter)}
}
 
func (r *Resource) doWork() {
    logger.Printf("%s doing work", r.id)
}
 
```


Let’s demo sync.Pool!


### Demo 1️⃣ 


In the first demo, we get the resource from the pool, do some work and then put it back. By doing this one step at the time in the end we’ll end with just one **Resource** instance.


```
func demo1() {
	println("demo1")
	theResourcePool := sync.Pool{New: func() any {
		return NewResource()
	}}

	for i := 0; i < 10; i++ {
		item := theResourcePool.Get().(*Resource)
		item.doWork()
		theResourcePool.Put(item)
	}

	println("done", counter)
}
```


Output


```
demo1
2022/08/17 22:38:59 NewResource called
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
2022/08/17 22:38:59 Resource-1 doing work
done 1
```


Resource-1 is the only instance that does work.


### Demo 2️⃣


In demo2 we spawn 10 goroutines, that use the pool. Since all goroutines start roughly at the same time and require a resource to **doWork**, in the end the pool will have 10 Resource instances.


```
func demo2() {
	println("demo2")
	wg := sync.WaitGroup{}
	theResourcePool := sync.Pool{New: func() any {
		return NewResource()
	}}

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			item := theResourcePool.Get().(*Resource)
			item.doWork()
			theResourcePool.Put(item)
		}()

	}
	wg.Wait()

	println("done", counter)
}
```


Output


```
demo2
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-3 doing work
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-4 doing work
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-5 doing work
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-6 doing work
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-7 doing work
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-8 doing work
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 NewResource called
2022/08/17 22:41:12 Resource-1 doing work
2022/08/17 22:41:12 Resource-2 doing work
2022/08/17 22:41:12 Resource-9 doing work
2022/08/17 22:41:12 Resource-10 doing work
done 10
```


### Demo 3️⃣


In demo3 doing the same thing we did in demo2 with some random sleeps in between, some goroutines are faster and others are slower. The faster goroutines will also return the resource faster to the pool and slower goroutines which start at a later time will reuse the resource instead of creating a new one.


```
func demo3() {
	println("demo2")
	wg := sync.WaitGroup{}
	theResourcePool := sync.Pool{New: func() any {
		return NewResource()
	}}

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			time.Sleep(time.Duration(rand.Intn(900)+100) * time.Millisecond)
			item := theResourcePool.Get().(*Resource)
			item.doWork()
			time.Sleep(time.Duration(rand.Intn(100)+100) * time.Millisecond)
			theResourcePool.Put(item)
		}()

	}
	wg.Wait()

	println("done", counter)
}
```


Output


```
demo2
2022/08/17 22:42:35 NewResource called
2022/08/17 22:42:35 Resource-1 doing work
2022/08/17 22:42:35 NewResource called
2022/08/17 22:42:35 Resource-2 doing work
2022/08/17 22:42:35 NewResource called
2022/08/17 22:42:35 Resource-3 doing work
2022/08/17 22:42:36 Resource-1 doing work
2022/08/17 22:42:36 Resource-2 doing work
2022/08/17 22:42:36 Resource-3 doing work
2022/08/17 22:42:36 Resource-1 doing work
2022/08/17 22:42:36 NewResource called
2022/08/17 22:42:36 Resource-4 doing work
2022/08/17 22:42:36 NewResource called
2022/08/17 22:42:36 Resource-5 doing work
2022/08/17 22:42:36 Resource-2 doing work
done 5
```


Only 5 Resource instances have been created at this time.


## Conclusion


The object pool pattern is a great pattern when you need to reuse an instance of an object. Constructing the object every time can be slow.


In Go we have [sync.pool](https://pkg.go.dev/sync#Pool) which implements the Object Pool pattern for us, we just need to give it a New function that returns a pointer.


Thanks for reading! 📚


## References


- https://pkg.go.dev/sync#Pool


## Full Code


```
package main

import (
	"fmt"
	"log"
	"math/rand"
	"sync"
	"time"
)

var logger = log.Default()
var counter = 0

type Resource struct {
	id string
}

func NewResource() *Resource {
	logger.Printf("NewResource called")
	counter += 1
	return &Resource{id: fmt.Sprintf("Resource-%d", counter)}
}

func (r *Resource) doWork() {
	logger.Printf("%s doing work", r.id)
}

func demo1() {
	println("demo1")
	theResourcePool := sync.Pool{New: func() any {
		return NewResource()
	}}

	for i := 0; i < 10; i++ {
		item := theResourcePool.Get().(*Resource)
		item.doWork()
		theResourcePool.Put(item)
	}

	println("done", counter)
}

func demo2() {
	println("demo2")
	wg := sync.WaitGroup{}
	theResourcePool := sync.Pool{New: func() any {
		return NewResource()
	}}

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			item := theResourcePool.Get().(*Resource)
			item.doWork()
			theResourcePool.Put(item)
		}()

	}
	wg.Wait()

	println("done", counter)
}

func demo3() {
	println("demo2")
	wg := sync.WaitGroup{}
	theResourcePool := sync.Pool{New: func() any {
		return NewResource()
	}}

	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			time.Sleep(time.Duration(rand.Intn(900)+100) * time.Millisecond)
			item := theResourcePool.Get().(*Resource)
			item.doWork()
			time.Sleep(time.Duration(rand.Intn(100)+100) * time.Millisecond)
			theResourcePool.Put(item)
		}()

	}
	wg.Wait()

	println("done", counter)
}

func main() {
	demo1()
	//demo2()
	//demo3()
}
```