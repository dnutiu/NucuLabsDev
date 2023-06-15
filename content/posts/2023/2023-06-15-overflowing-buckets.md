---
author: ["Denis Nutiu"]
categories:
- "Programming"
- "Python"
date: '2023-06-15'
layout: post
permalink: /2023/06/15/overflowing-buckets
tags:
- python
- programming
- leetcode
- "data structures"
title: "Overflowing Buckets"
---

Hello everyone 👋,

Today I want to share with you a problem I've encountered along my journey.

The problem is fairly simple:

- You have a finite number of water units.
- You have a finite number of buckets.
- You need to distribute the water units into the buckets.
- Each bucket has a maximum capacity.
- When a bucket is full, the water overflows into the next two buckets by splitting evenly.
- You need to print the number of the buckets that are full.

The following diagram illustrates the problem:

![overflowing buckets](/hugo-content/2023-06/buckets_diagram.png)

If you would like to attempt to solve the problem by yourself please stop reading here. 
And try it 😄.

I had this problem during a whiteboard interview and I did not manage to solve it, as I was writing
the class the interviewer steered me in another direction and that threw my thinking off. I'm a big fan of
Object Oriented design.

My time was limited because the statement of the problem was very vague, and most of the time I was trying to
identify what to solve for.

Anyways, I hope you'll have better luck in whiteboard interview and hopefully you get to do them in your IDE. 
I've had no problem passing TopTal coding assignments when they let me do it in my IDE.

The `Run`, `Evaluate`, `Improve` feedback loop is very important for me as a programmer. I work daily in my IDE writing
hundreds of lines of code, suddenly getting a problem to solve on paper does throw me off. 

Even in complex projects when I want to test my idea I write a simple unit-test, this can be done only if the project 
is structured well and the code is designed for unit testing, poor code quality and projects are usually testable. 
If you want to learn more about unit-testing I cannot recommend this book enough: 
[Growing Object-Oriented Software, Guided by Tests](https://www.goodreads.com/book/show/4268826-growing-object-oriented-software-guided-by-tests)

Below you can find my Python solution to the problem.

---

```python
import dataclasses


@dataclasses.dataclass
class Bucket:
    id: int
    left: "Bucket" = None
    right: "Bucket" = None
    current_capacity: float = 0
    max_capacity: int = 10

    def fill(self, unit: float):
        if self.current_capacity < self.max_capacity:
            self.current_capacity += unit
            return unit
        else:
            units_needed = 0
            if self.right:
                units_needed += self.right.fill(unit / 2)

            if self.left:
                units_needed += self.left.fill(unit / 2)

            return units_needed

    def print_bucket(self):
        print(f"Bucket: #{self.id} has capacity {self.current_capacity}")

        if self.left:
            self.left.print_bucket()

        if self.right:
            self.right.print_bucket()


if __name__ == '__main__':
    b5 = Bucket(5, left=Bucket(7))
    root = Bucket(1, left=Bucket(2, left=Bucket(4), right=b5), right=Bucket(3, left=b5, right=Bucket(6)))
    root.print_bucket()

    print("=====================")

    number_of_units = 51
    while number_of_units > 0:
        units_used = root.fill(1)
        if units_used == 0:
            break
        number_of_units -= units_used

    root.print_bucket()
```

Of course my solution is not perfect, but if you have any suggestions please let me know.

I hope you enjoyed this post and I'll see you in the next one!

Thank you for reading! 📚
