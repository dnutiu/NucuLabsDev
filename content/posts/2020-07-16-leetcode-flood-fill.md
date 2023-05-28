---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Python
date: '2020-07-16T22:31:37+00:00'
guid: http://nuculabs.dev/?p=1010
id: 1010
layout: post
permalink: /2020/07/16/leetcode-flood-fill/
tags:
- flood fill
- leetcode
title: 'LeetCode: Flood Fill'
---
Hello,


Here’s my solution for the flood fill problem, found on LeetCode.


If you want me to write about certain topics please let me know in the comments, thank you!


Link to the problem: 


```
"""
An image is represented by a 2-D array of integers, each integer representing the pixel
value of the image (from 0 to 65535).


Given a coordinate (sr, sc) representing the starting pixel (row and column) of the flood fill,
and a pixel value newColor, "flood fill" the image.


To perform a "flood fill", consider the starting pixel, plus any pixels connected 4-directionally
to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally
to those pixels (also with the same color as the starting pixel), and so on. Replace the color of all
of the aforementioned pixels with the newColor.


At the end, return the modified image.
"""
from typing import List, Tuple




class Solution:
    def __init__(self):
        self.visited = set()


    def _set_pixel(self, image: List[List[int]], point: Tuple[int, int], value: int):
        try:
            image[point[0]][point[1]] = value
        except IndexError:
            pass


    def _get_pixel(self, image: List[List[int]], point: Tuple[int, int]):
        if point[0] < 0 or point[1] < 0:
            return None
        try:
            return image[point[0]][point[1]]
        except IndexError:
            return None


    def _floodFill(self, image: List[List[int]], point: Tuple[int, int], color: int, newColor: int) -> List[List[int]]:
        pixel = self._get_pixel(image, point)
        if pixel is not None and pixel == color and point not in self.visited:
            self.visited.add(point)
            self._set_pixel(image, point, newColor)
            self._floodFill(image, (point[0], point[1] + 1), color, newColor)
            self._floodFill(image, (point[0], point[1] - 1), color, newColor)
            self._floodFill(image, (point[0] + 1, point[1]), color, newColor)
            self._floodFill(image, (point[0] - 1, point[1]), color, newColor)


    def floodFill(self, image: List[List[int]], sr: int, sc: int, newColor: int) -> List[List[int]]:
        point = (sr, sc)
        pixel = self._get_pixel(image, point)
        self.visited = set()
        if pixel is not None:
            self._floodFill(image, point, pixel, newColor)
        return image


if __name__ == '__main__':
    s = Solution()


    out = s.floodFill([[1,1,1],[1,1,0],[1,0,1]], 1, 1, 2)
    print("Output", out)
    assert out == [[2,2,2],[2,2,0],[2,0,1]]


    out = s.floodFill([[0,0,0],[0,0,0]], 0, 0, 2)
    print("Output", out)
    assert out == [[2,2,2],[2,2,2]]


    out = s.floodFill([[0,0,0],[0,1,1]], 1, 1, 1)
    print("Output", out)
    assert out == [[0, 0, 0], [0, 1, 1]]


```