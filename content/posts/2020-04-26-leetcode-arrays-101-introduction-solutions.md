---
advanced_seo_description:
- ''
- ''
amp_status:
- ''
- ''
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2020-04-26T20:54:43+00:00'
guid: http://nuculabs.dev/?p=949
id: 949
layout: post
permalink: /2020/04/26/leetcode-arrays-101-introduction-solutions/
spay_email:
- ''
- ''
tags:
- leetcode
- Python
title: 'LeetCode: Arrays 101: Introduction &#8211; Solutions'
---
Hello,


LeetCode is a good place to practice programming by solving problems of all levels!


After you solve a problem, you’ll get access to all submitted solutions, sorted by time and memory usage. That’s a nice way to compare your code to others and see what you did well and where you can improve.


Here’s my solutions for the [Arrays 101: Introduction](https://leetcode.com/explore/learn/card/fun-with-arrays/) card:


## Max Consecutive Ones




```
class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        max_counter = 0
        counter = 0
        for i in nums:
            if i == 1:
                counter += 1
            else:
                if counter > max_counter:
                    max_counter = counter
                counter = 0
        if counter > max_counter:
            max_counter = counter
        return max_counter
```


## Find Numbers with Even Number of Digits




```
class Solution:


    @staticmethod
    def is_even(value):
        return len(str(value)) % 2 == 0


    def findNumbers(self, nums: List[int]) -> int:
        return_value = 0
        for number in nums:
            if self.is_even(number):
                return_value += 1
        return return_value
```


## Squares of a Sorted Array




*When writing code like this be careful because you’re altering the **A** list in place.*


```
class Solution:
    def sortedSquares(self, A: List[int]) -> List[int]:
        for index, num in enumerate(A):
            A[index] = num ** 2
        return sorted(A)
```


Thanks for reading!


If you like what I’m writing, don’t forget to subscribe! 🙂


[jetpack_subscription_form show_subscribers_total=”false” button_on_newline=”true” custom_font_size=”16px” custom_border_radius=”0″ custom_border_weight=”1″ custom_padding=”15″ custom_spacing=”10″ submit_button_classes=”has-text-color has-background-color has-background has-primary-background-color” email_field_classes=”” show_only_email_and_button=”true”]