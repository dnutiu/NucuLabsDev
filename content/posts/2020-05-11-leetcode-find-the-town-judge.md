---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2020-05-11T20:05:11+00:00'
guid: http://nuculabs.dev/?p=1007
id: 1007
layout: post
permalink: /2020/05/11/leetcode-find-the-town-judge/
tags:
- algorithms
- leetcode
- Python
title: 'LeetCode: Find The Town Judge'
---
Hello


In this article I will present you my Python3 solution for the following problem which I found on LeetCode: [find-the-town-judge](https://leetcode.com/problems/find-the-town-judge/).


*Note: The solution is on the second page, so you won’t get spoiled if you want to attempt to solve the problem by yourself. Have fun!*


```
from collections import defaultdict
from typing import List
"""
In a town, there are N people labelled from 1 to N.
There is a rumor that one of these people is secretly the town judge.


If the town judge exists, then:


    The town judge trusts nobody.
    Everybody (except for the town judge) trusts the town judge.
    There is exactly one person that satisfies properties 1 and 2.


You are given trust, an array of pairs trust[i] = [a, b] representing that the person labelled a
trusts the person labelled b.


If the town judge exists and can be identified, return the label of the town judge.  Otherwise, return -1.
"""


class Solution:
    def _filterForJudge(self, N, trust_dict):
        no_trustees = None
        for i in range(1, N + 1):
            person_trusted = trust_dict.get(i, set())
            if len(person_trusted) == 0:
                if no_trustees is None:
                    no_trustees = i
                else:
                    return None
        return no_trustees


    def findJudge(self, N: int, trust: List[List[int]]) -> int:
        trust_dict = defaultdict(set)
        for i in trust:
            trust_dict[i[0]].add(i[1])


        # The town judge trusts nobody.
        no_trustee = self._filterForJudge(N, trust_dict)
        if no_trustee:
            # Everybody trusts the town judge.
            people_who_trust = 0
            for p in trust_dict.items():
                # Check if the person trusts the town judge.
                if no_trustee in p[1]:
                    people_who_trust += 1
            if people_who_trust == N-1:
                return no_trustee
        return -1


if __name__ == '__main__':
    s = Solution()


    print(s.findJudge(2, [[1,2]]))
    print(s.findJudge(3, [[1,3],[2,3]]))
    print(s.findJudge(3, [[1,3],[2,3],[3,1]]))
    print(s.findJudge(3, [[1,2],[2,3]]))
    print(s.findJudge(4, [[1,3],[1,4],[2,3],[2,4],[4,3]]))
```


Thanks for reading!