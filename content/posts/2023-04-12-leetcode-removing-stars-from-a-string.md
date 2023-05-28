---
author: ["Denis Nu\u021Biu", "Paula Rusti"]
categories:
- Programming
- Tutorial
date: '2023-04-12T20:43:49+00:00'
layout: post
permalink: /2023/04/12/leetcode-removing-stars-from-a-string/
tags:
- leetcode
- rust
- python
title: "LeetCode: Removing Stars From a String & Simplify Path"
---

Hello 👋,

I’ve started doing some LeetCode dailies with Paula and I’d like to share our solutions for the last two problems.

https://leetcode.com/problems/removing-stars-from-a-string/

https://leetcode.com/problems/simplify-path/

I also want to warmly welcome the new nuculabs.dev subscribers! Thank you for subscribing!

If you want to attempt the problems by yourself then stop reading otherwise you’ll be spoiled with solutions.

# Removing Stars From a String

In this problem you are given a string with stars ‘*’ and when you encounter a start you’ll need to remove the start along with the character to it’s left.

The full problem statement can be found here: [removing-stars-from-a-string](https://leetcode.com/problems/removing-stars-from-a-string/).

The solution involves using a simple stack:

---

```python
class Solution:
    def removeStars(self, s: str) -> str:
        list_s = []
        for i, char in enumerate(s):
            if char == "*":
                list_s.pop()
            else:
                list_s.append(char)
        return "".join(list_s)
```

# Simplify Path

In this problem you are given a unix path and you need to simplify it, for example the path "/a//b////c/d//././/.." will be simplified to "/a/b/c".

The solution involves using a simple stack:

```python
class Solution:
    def remove_duplicated_slashes(self, path: str):
        clean_path = []
        last_char = None
        for s in path:
            if s == "/" and last_char == "/":
                continue
            clean_path.append(s)
            last_char = s
        return "".join(clean_path)

    def simplifyPath(self, path: str) -> str:
        # remove duplicate slashes
        simplified_path = []
        clean_path = self.remove_duplicated_slashes(path)
        parts = clean_path.split("/")
        for part in parts:
            if part == "." or part == "":
                continue
            elif part == "..":
                if len(simplified_path) > 0:
                    simplified_path.pop()
            else:
                simplified_path.append(part)

        return "/" + "/".join(simplified_path)
```

Paula’s solution is simpler and a cleaner in my opinion:

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        dirs = path.split('/')
        dirs = [dir for dir in dirs if len(dir) > 0]
        stack = []
        for dir in dirs:
            if dir == '.':
                continue
            else:
                if dir != '..':
                    stack.append(dir)
                else:
                    if len(stack) == 0:
                        stack.append('/')
                    stack.pop()
        # print(stack[::-1])
        return '/'+"/".join(stack)
```

Thanks for reading! 🍻