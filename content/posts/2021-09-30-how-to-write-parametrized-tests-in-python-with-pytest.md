---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Video
date: '2021-09-30T19:15:28+00:00'
guid: https://nuculabs.dev/?p=1751
id: 1751
image: /wp-content/uploads/2021/09/pexels-photo-414171.jpeg
layout: post
permalink: /2021/09/30/how-to-write-parametrized-tests-in-python-with-pytest/
tags:
- pytest
- Python
- tests
- unit testing
title: "How to write parametrized tests in Python with pytest \U0001F3A5"
---
Hi 👋


Welcome to another video tutorial on how to write parametrized tests in Python using [pytest](https://docs.pytest.org/en/6.2.x/).


{{< youtube o2LLi_iiz-k >}}

If you want to follow along, here’s the code that I’ve tested in the video.


```
from typing import List




class Solution:
    def move_zeroes(self, nums: List[int]) -> None:
        last_zero = 0
        index = 0
        while index < len(nums):
            if nums[index] != 0:
                nums[last_zero], nums[index] = nums[index], nums[last_zero]
                last_zero += 1
            index += 1




def main():
    solution = Solution()
    arr = [1,0,1]
    solution.move_zeroes(arr)
    print(arr)




if __name__ == '__main__':
    main()
```


Thanks for watching! 😄