---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Python
date: '2020-04-30T18:30:00+00:00'
guid: http://nuculabs.dev/?p=976
id: 976
layout: post
permalink: /2020/04/30/leetcode-arrays-101-inserting-items-into-an-array/
tags:
- arrays
- leetcode
title: 'LeetCode: Arrays 101: Inserting Items Into an Array'
---
Hello,


Here are my solutions for the second part of the card: Arrays 101, from LeetCode.


## Duplicate Zeroes


Given an array of integers, remove duplicate zeroes and shift the remaining elements.




```
class Solution:
    def duplicateZeros(self, arr: List[int]) -> None:
        """
        Do not return anything, modify arr in-place instead.
        """
        index = 0
        arr_length = len(arr)
        while index < arr_length:
            if arr[index] == 0:
                arr.insert(index, 0)
                arr.pop()
                index += 1
            index += 1
```


##  Merge Sorted Array


Given two sorted arrays, merge them together into nums1.




This problem wasn’t very Pythonic and I actually want to practice my problem solving skills together with my Python skills, so, I’ve took the liberty to modify the problem a little:


```
from typing import List


class Solution:
    def merge(self, nums1: List[int], nums2: List[int]) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        n = len(nums2)
        # remove 0 from nums1
        nums2_index = 0

        for i, num in enumerate(nums1):
            # We've reached the end of nums2, means we're merged.
            if nums2_index == n:
                break
            if nums2[nums2_index] <= num:
                nums1.insert(i, nums2[nums2_index])
                nums2_index += 1

        # If there's anything left in nums2 we'll add it to the end
        for item in nums2[nums2_index:]:
            nums1.append(item)


if __name__ == '__main__':
    s = Solution()
    a = [1,2,3]
    s.merge(a, [2,5,6])
    print(a)
```


Thank you!


[jetpack_subscription_form show_subscribers_total=”false” button_on_newline=”true” custom_font_size=”16px” custom_border_radius=”0″ custom_border_weight=”1″ custom_padding=”15″ custom_spacing=”10″ submit_button_classes=”has-text-color has-background-color has-background has-primary-background-color” email_field_classes=”” show_only_email_and_button=”true”]