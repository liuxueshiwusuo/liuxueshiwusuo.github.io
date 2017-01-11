---
title: Segment Tree
date: 2016-05-18 12:02:00
category: 解题报告
tags: LeetCode
---

**Concepts**
---
Segment tree is a data structure that stores interval, mainly realize a maximum querying time consuming of log(n).
<!--more-->

                  [0, 3, max=4]
                 /             \
          [0,1,max=4]        [2,3,max=3]
          /         \        /         \
   	[0,0,max=1] [1,1,max=4] [2,2,max=2], [3,3,max=3]
The basic structure loos like this. Every interval is cut into two parts (subnodes), [start, mid] and [mid + 1, end]. It is extremely useful when we want to find the minimum/maximum number in specified intervals.

A segment tree for a set I of n intervals uses O(n log n) storage and can be built in O(n log n) time.[^1]

[^1]: https://en.wikipedia.org/wiki/Segment_tree

**Typical problem**
---
#### **[Interval Minimum Number](http://www.lintcode.com/en/problem/interval-minimum-number/)**

Given an integer array (index from 0 to n-1, where n is the size of this array), and an query list. Each query has two integers [start, end]. For each query, calculate the minimum number between index start and end in the given array, return the result list.

#### Example

For array [1,2,7,8,5], and queries [(1,2),(0,4),(2,4)], return [2,1,5]

#### Code

```python
"""
Definition of Interval.
class Interval(object):
    def __init__(self, start, end):
        self.start = start
        self.end = end
"""

class SegmentTree(object):
    def __init__(self, start, end, min):
        self.start, self.end = start, end
        self.min = min

class Solution:	
    """
    @param A, queries: Given an integer array and an Interval list
                       The ith query is [queries[i-1].start, queries[i-1].end]
    @return: The result list
    """
    
    def intervalMinNumber(self, A, queries):
        # write your code here
        # build segment tree first
        root = self.buildTree(A, 0, len(A) - 1)
        return self.search(root, queries)
        
    # @param A, start, end
    # return: segment tree root
    def buildTree(self, A, start, end):
        if start > end:
            return None
        node = SegmentTree(start, end, A[start])
        if start == end:
            return node
        mid = (start + end) / 2
        node.left = self.buildTree(A, start, mid)
        node.right = self.buildTree(A, mid + 1, end)
        if node.left is not None:
            if node.left.min < node.min or node.right.min < node.min:
                node.min = min(node.left.min, node.right.min)
        return node
        
    # @param A, queries
    # @return: The result list
    def search(self, root, queries):
        li = []
        for query in queries:
            start = query.start
            end = query.end
            li.append(self.searchForMin(root, start, end))
        return li
    
    def searchForMin(self, root, start, end):
        if (end < root.start or start > root.end):
            return sys.maxint
        if (start <= root.start and end >= root.end):
            return root.min
        return min(self.searchForMin(root.left, start, end), 
                   self.searchForMin(root.right, start, end))
```

