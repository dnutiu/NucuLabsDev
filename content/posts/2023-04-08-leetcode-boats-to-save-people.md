---
author: ["Denis Nu\u021Biu", "Paula Rusti"]
categories:
- Programming
- Tutorial
date: '2023-04-08T20:43:49+00:00'
layout: post
permalink: /2023/04/08/leetcode-boats-to-save-people/
tags:
- leetcode
- rust
- python
title: "LeetCode: Boats to Save People"
---

# Introduction

Hello everyone! 👋

I’ve been practicing with some Leetcode daily challenges lately and since I’ve recently moved to Substack I wanted to make my first post here 😁

## Boats to Save People

The problem [Boats to Save People](https://leetcode.com/problems/boats-to-save-people/) is solved using a [Greedy](https://en.wikipedia.org/wiki/Greedy_algorithm) approach.

Given a number of people and their weights, along with a carry limit for the boat.

You must find the number of boats needed to carry the people. A boat can carry a maximum of two people at a time.

To solve for this constraint, we would first sort the array containing the weights then attempt to pair the heaviest person with the lightest person. If their weight is below the or equal to the carry limit you remove the heaviest and lightest person from the list of people and add a count of one to the boats needed to carry people.

If you can’t make the pair, then you’ll put the heaviest person on the boat and start looking for another pair. You will also add one to the number of boats needed.

### Rust Solution

```rust
impl Solution {
    pub fn num_rescue_boats(people: Vec<i32>, limit: i32) -> i32 {
        let mut i = 0;
        let mut j = people.len() - 1;
        let mut boats = 0;

        let mut sorted_people = people;
        sorted_people.sort_unstable();

        while i <= j {
            boats += 1;
            if sorted_people[i] + sorted_people[j] <= limit {
                i += 1
            }
            if j == 0 {
                break
            }
            j -= 1;
        }

        return boats;
    }
}
```

### Python Solution

```python
class Solution:
    def numRescueBoats(self, people: List[int], limit: int) -> int:
        people_size = len(people)
        if people_size == 1:
            return 1

        people.sort()
        boats_needed = 0
        i = 0
        j = people_size - 1
        while i <= j:
            # We found a pair of people
            if people[i] + people[j] <= limit:
                boats_needed += 1
                i += 1
                j -= 1
                continue

            j -= 1
            boats_needed += 1

        return boats_needed
```

Thank you for reading!