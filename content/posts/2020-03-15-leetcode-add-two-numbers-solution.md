---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Python
date: '2020-03-15T17:45:54+00:00'
guid: https://nuculabs.dev/?p=895
id: 895
layout: post
permalink: /2020/03/15/leetcode-add-two-numbers-solution/
tags:
- leetcode
title: 'LeetCode: Add Two Numbers Python 3 iterative solution'
---
\[**[Problem Link](https://leetcode.com/problems/add-two-numbers/)**\]


Hello,


Here’s my solution for the add two numbers problem on LeetCode.


**Example:**


```
Input:
 (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output:
 7 -> 0 -> 8
Explanation:
 342 + 465 = 807.
```


When doing problems on websites such as LeetCode, I like to code in my own IDE, that’s why you’ll see some additional helper functions besides the solution code.


```
class Solution(object):
    def _get_append_func(self, root):
        _root = root
        def __add_to_list(value):
            nonlocal _root
            _root.next = ListNode(value)
            _root = _root.next
        return __add_to_list


    def _compute(self, result, base=10):
        carry = result // base
        rest = result % base
        return carry, rest


    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        root_node = None
        root_append_func = None


        term_1 = l1
        term_2 = l2
        saved_carry = 0
        while True:
            # end of expression ; add cary if any
            if term_1 is None and term_2 is None:
                if saved_carry > 0:
                    root_append_func(saved_carry)
                break


            # We haven't reached the end, we assume that whatever term is left is 0
            if term_1 is None:
                term_1 = ListNode(0)
            if term_2 is None:
                term_2 = ListNode(0)


            # add the two terms
            term_sum_carry, term_sum_no_carry = self._compute(term_1.val + term_2.val)


            # add sum to current carry
            next_carry, total_with_carry = self._compute(term_sum_no_carry + saved_carry)


            # save carry
            saved_carry = term_sum_carry + next_carry


            # save total
            if not root_node:
                root_node = ListNode(total_with_carry)
                root_append_func = self._get_append_func(root_node)
            else:
                root_append_func(total_with_carry)


            # move on
            term_1 = term_1.next
            term_2 = term_2.next


        return root_node
```


My solution isn’t that great because of the **\_add\_to\_list** function which iterates the root\_node to append the next value. I have no idea how to optimize that right now.


UPDATE:


I’ve learnt about **nonlocal** and found a way to slightly optimize my code:


```
    def _get_append_func(self, root):
        _root = root
        def __add_to_list(value):
            nonlocal _root
            _root.next = ListNode(value)
            _root = _root.next
        return __add_to_list
```


Thanks for reading!