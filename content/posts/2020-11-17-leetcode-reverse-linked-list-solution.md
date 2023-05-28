---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2020-11-17T22:59:44+00:00'
guid: http://nuculabs.dev/?p=1180
id: 1180
layout: post
permalink: /2020/11/17/leetcode-reverse-linked-list-solution/
tags:
- algorithms
- leetcode
- linked list
- solution
title: 'LeetCode: Reverse Linked List Solution and Explanation'
---
Hi,


In this article I will explain my solution for the following LeetCode problem: [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/).


If you’re interested in solving this problem then please try to spend at least one hour or more on it before looking at my solution.


To help you solve this problem I’ve created the following table:


| Current | Prev |
|---|---|
| 1 | NULL |
| 2 | 1 |
| 3 | 2 |
| NULL | 3 |


Think about how you can use this table to write an iterative or recursive algorithm that reverses the linked list.


- - - - - -


## The Solution


Bellow you’ll find my iterative and recursive solutions with explanations. The solutions are written in C#.


### The Recursive Solution


If you look at the table I made, you may notice that in order to reverse the list all you need to do is make the current node to point to the previous node during each recursive call, then move to the next node.


The stopping condition happens when the current node is null. Then you need to return the previous node, which points to the previous node, which points to the previous and so on. You’ve got a reversed linked list.


The complexity will be O(n) for time, because we need to cover the whole list and O(n) for space, because for every call made the program will allocate a new stack frame.


### The Iterative Solution


The iterative solution is similar with the recursive one, except it doesn’t allocate any extra space, it just iterates over the list, modifying it in place.


It updates the current node to the previous one and at the end it returns the last node.


The complexity is O(n) for time and O(1) for space.


- - - - - -


```
using System;


namespace LeetCode.November
{
    public class ReverseLinkedListProb
    {
        public class ListNode
        {
            public int val;
            public ListNode next;


            public ListNode(int val = 0, ListNode next = null)
            {
                this.val = val;
                this.next = next;
            }
        }
 
        // Space O(1) ; Time O(n)
        public ListNode ReverseListIterative(ListNode head)
        {
            ListNode prev = null;
            while (head != null)
            {
                // save next
                var next = head.next;
                // next -> prev
                head.next = prev;
                // prev -> head
                prev = head;
                // head -> next
                head = next;
            }


            return prev;
        }


        // Space O(n) ; Time O(n)
        public ListNode ReverseListRecursion(ListNode head, ListNode prev)
        {
            if (head == null)
            {
                return prev;
            }
            var next = head.next;
            head.next = prev;
            return ReverseListRecursion(next, head);
        }


        public ListNode ReverseList(ListNode head)
        {
            var last = ReverseListIterative(head);
            return last;
        }




        public static void Test()
        {
            var problem = new ReverseLinkedListProb();
            var r = problem.ReverseList(new ListNode(1, new ListNode(2, new ListNode(3))));
            while (r != null)
            {
                Console.Write($"{r.val} ");
                r = r.next;
            }
        }
    }
}
```


Thanks for reading!