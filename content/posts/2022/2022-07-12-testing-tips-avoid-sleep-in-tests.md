---
author: "Denis Nu\u021Biu"
categories:
- Featured
- Programming
- Tutorial
date: '2022-07-12T20:15:00+00:00'
guid: https://nuculabs.dev/?p=2273
id: 2273
layout: post
permalink: /2022/07/12/testing-tips-avoid-sleep-in-tests/
tags:
- Golang
- polling
- testing
- testing tips
title: 'Testing Tips: Avoid sleep in tests'
weight: 100
showtoc: false
---
Hi 👋,


In this article I wanna show a testing tip that I’ve recently learned myself by reading [Software Engineering at Google: Lessons Learned from Programming Over Time](https://www.goodreads.com/en/book/show/48816586-software-engineering-at-google). The technique improved the way I write unit tests.


When I’m writing bigger unit tests, I have execute something in the background, like for example **publishing a message to a message broker**, 
wait for the message to be published
 and then 
consume it
 to test that what I published is correct.


When 
waiting for the message to be published
 or any other operation that required waiting in tests I used to call a **sleep** function, for **a second** or **two**, this is decent for few tests but if your tests grow then this approach does not scale well. Imagine if you’re having 50 tests and each test **sleeps for one second**, it would take at least 50 seconds to run the test suite, which is a lot of wasted time.


The better approach is to use a **timeout** and **polling**, you can **
poll at every millisecond to see if your test has done what you wanted to do instead of sleeping
**, this will improve the tests and reduce the execution time by a lot!


Let’s demonstrate this will a small example using the [Golang](https://go.dev/) programming language, I’m not going to use any external dependencies to demonstrate this technique but you can apply it everywhere you’re calling something that blocks or if you need to wait for something.


What we’re going to test is a simple struct with a method that blocks and modifies a field.


```
import (
	"math/rand"
	"time"
)

type SystemUnderTest struct {
	Result string
}

func (s *SystemUnderTest) SetResult() {
	go func() {
		time.Sleep(time.Duration(rand.Intn(3000)) * time.Millisecond)
		s.Result = "the_result"
	}()
}

func (s *SystemUnderTest) GetData() string {
	time.Sleep(time.Duration(rand.Intn(3000)) * time.Millisecond)
	return "the_data"
}
```


This is the **
not ideal way of testing it
**:


```
// A not very ideal way to test SetResult
func Test_SystemUnderTest_SetResult_NotIdeal(t *testing.T) {
	sut := SystemUnderTest{}
	sut.SetResult()

	time.Sleep(4 * time.Second)

	if sut.Result != "the_result" {
		t.Fatalf("Result not equal, want %s got %s", "the_result", sut.Result)
	}
}
```


SetResults takes between 0 to 3 seconds to run, since we’re waiting for the result we’re sleeping for 4 seconds.


```
=== RUN   Test_SystemUnderTest_SetResult_NotIdeal
--- PASS: Test_SystemUnderTest_SetResult_NotIdeal (4.00s)
PASS
```


A better way is to 
**write a simple loop and poll for the result**
:


```
// A better way of testing the code
func Test_SystemUnderTest_SetResult(t *testing.T) {
	sut := SystemUnderTest{}
	sut.SetResult()

	passedMilliseconds := 0
	for {
		if passedMilliseconds > 4000 {
			t.Fatalf("timeout reached")
		}
		passedMilliseconds += 1
		time.Sleep(1 * time.Millisecond)
		if sut.Result != "" {
			break
		}
	}
	if sut.Result != "the_result" {
		t.Fatalf("Result not equal, want %s got %s", "the_result", sut.Result)
	}
}
```


Writing a loop and polling for the result will make the test more complex but it will execute faster. In this case the benefits outweigh the downsides.


```
=== RUN   Test_SystemUnderTest_SetResult
--- PASS: Test_SystemUnderTest_SetResult (2.08s)
PASS
```


If the language permits we can also use channels, let’s change the following function that returns a result after a random amount of time and test it.


```
func Test_SystemUnderTest_GetData(t *testing.T) {
	sut := SystemUnderTest{}

	timeoutTicker := time.NewTicker(5 * time.Second)
	result := make(chan string)

	// Get result when ready
	go func() {
		result <- sut.GetData()
	}()

	select {
	case <-timeoutTicker.C:
		t.Fatal("timeout reached")
	case actual := <-result:
		if actual != "the_data" {
			t.Fatalf("Data not equal, want: %s, got %s", "the_data", actual)
		}
	}
}
```


We avoided writing a loop with the use of a [ticker](https://gobyexample.com/tickers) and [select](https://go.dev/tour/concurrency/5).


In another case you may need to test HTTP calls on the local machine or any other library. **Look for timeout options**.


Go’s HTTP library let’s you specify a custom timeout for every call you make:


```
	client := http.Client{
		Timeout: 50 * time.Millisecond,
	}
	response, err := client.Get("http://localhost:9999/metrics")
	...
```


## In Conclusion


Avoid the use of sleep in tests, try polling for the result instead or check if the blocking functions have parameters or can be configured to stop the execution after a timeout period.


Thanks for reading and I hope you’ve enjoyed this article! 🍻