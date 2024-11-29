---
title: "The Go *Options pattern"
author: [ "Denis Nutiu" ]
date: '2024-11-26'
categories:
  - "Golang"
  - "Design Patterns"
permalink: /2024/11/26/the-go-options-pattern
tags:
  - design patterns
  - programming
  - golang
layout: post
---

# Introduction

Hello everyone! 👋

In this article I'll present you the options pattern in Golang. The pattern is useful when you want to create a function
that takes different parameters as an option.

# Code Study: Building a rocket

The following code defines a Rocket struct with 3 fields and a NewRocket function which builds an instance
of the Rocket.

```golang
// Rocket models a rocket.
type Rocket struct {
	name         string
	nose         Nose
	fuelCapacity int
}

// NewRocket returns a new rocket instance.
func NewRocket(name string, nose Nose, fuelCapacity int) Rocket {
	return Rocket{
		name:         name,
		nose:         nose,
		fuelCapacity: fuelCapacity,
	}
}
```

To use the NewRocket function one would have to write something like this:

```golang
func main() {
	rocket := NewRocket("Techno", PointyNose, 100)

	println(rocket.String())
}
```

This is straightforward, but what if we'd want the name of the rocket, nose and fuel capacity to be optional parameters?

We could rewrite the `NewRocket` function to take pointers instead of literals and if we do so we could build a 
default rocket with:

```golang
func main() {
	rocket := NewRocket(nil, nil, nil)

	println(rocket.String())
}
```

This is of course not very readable, my eyes start watering only by looking at this code.

When we want to modify the Rocket struct and add a new field, that is also instantiated we'd need to remember the 
position in our code and modify all the NewRocket function usages.

So far not using the options pattern for instantiating a Rocket has the following disadvantages:

- Ugly code.
- Hard to modify code.
- Need to refactor every usage of the function.

# The Options Pattern

The Options pattern is straightforward to implement and there are many variations, in this article we'll explore
one simple variation:

First, define the RocketOptions struct that takes the same configurable fields as the Rocket struct and defines
functions for instantiating them.

The fields are defined as pointers instead of literals because we want to treat the `nil` value as not user specified.

```golang
func NewRocketOptions() *RocketOptions {
	return &RocketOptions{
		name:         nil,
		nose:         nil,
		fuelCapacity: nil,
	}
}

func (o *RocketOptions) WithName(name string) *RocketOptions {
	o.name = &name
	return o
}

func (o *RocketOptions) WithNose(nose Nose) *RocketOptions {
	o.nose = &nose
	return o
}

func (o *RocketOptions) WithFuelCapacity(capacity int) *RocketOptions {
	o.fuelCapacity = &capacity
	return o
}
```

Next, modify the NewRocket function to use the RocketOptions struct like so:

```golang
// NewRocket returns a new rocket instance.
func NewRocket(options *RocketOptions) Rocket {
	var name string = "Unknown"
	if options.name != nil {
		name = *options.name
	}

	var nose Nose = PointyNose
	if options.nose != nil {
		nose = *options.nose
	}

	var fuelCapacity int = 100
	if options.fuelCapacity != nil {
		fuelCapacity = *options.fuelCapacity
	}

	return Rocket{
		name:         name,
		nose:         nose,
		fuelCapacity: fuelCapacity,
	}
}
```

The NewRocket now provides default values for the `nil` options and when they are specified it uses the user
provided option value.

## Other Variation

Another variation can be implemented like this:

```golang
type Nose string

const PointyNose Nose = "POINTY"
const RoundNose Nose = "ROUND"

type rocketOptions struct {
	name         string
	nose         Nose
	fuelCapacity int
}

type RocketOption func(options *rocketOptions)

// Rocket models a rocket.
type Rocket struct {
	options rocketOptions
}

func (r *Rocket) String() string {
	return fmt.Sprintf("%s rocket with %s nose and fuel capacity of %d", r.options.name, r.options.nose, r.options.fuelCapacity)
}

// NewRocket returns a new rocket instance.
func NewRocket(options ...RocketOption) Rocket {
	rocketOptions := rocketOptions{
		name:         "Unknown",
		nose:         PointyNose,
		fuelCapacity: 100,
	}

	for _, option := range options {
		option(&rocketOptions)
	}

	return Rocket{
		options: rocketOptions,
	}
}

func NameOption(name string) RocketOption {
	return func(options *rocketOptions) {
		options.name = name
	}
}

func main() {
	rocket := NewRocket()
	println(rocket.String())

	otherRocket := NewRocket(NameOption("Cool"))
	println(otherRocket.String())
}
```

# Conclusion

We've explored how the Options pattern can significantly enhance the design and usability of functions in Golang, 
particularly when dealing with multiple optional parameters.

Thank you for reading!