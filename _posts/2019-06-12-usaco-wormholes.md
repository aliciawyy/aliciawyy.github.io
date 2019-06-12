---
title: "USACO 1.4 Wormholes"
categories:
  - algorithms
tags:
  - usaco
  - puzzle
toc: true
use_math: true
---


# Idea

It takes me one whole afternoon to solve this puzzle. I got stuck at the Test
4 for missing the case that when several holes appear on the same line, if
one pair of wormholes is cut by a third one in the middle, the cow can be
untrapped.

Finally I think it's much easier to decompose the problem on two sub problems

## Enumerate all pairs sets

This is similar to [enumerate all permutations](https://www.geeksforgeeks.org/write-a-c-program-to-print-all-permutations-of-a-given-string/).
The latter is easy to solve with recursion. We just need to jump 2
 positions each time instead of 1.
 
I have added in this step some pruning, like if two holes are immediate neighbors on a horizontal line, we do not need to continue. Here we count the non cycle cases because I think it is faster than to count cycle with pruning.

In the worst case, we will have to enumerate all $(n - 1) * (n - 3) * ... * 1$ pairs sets.

## Check if cycle exists

For this part, I have used additional data structures to record the line (y equal) status. Otherwise, the idea is relatively straight forward: Let the cow start walking in +x direction at each wormhole and check if it can return to the starting hole. It can move either to the last hole of the line then continue without meeting any hole (untrapped) or return to the starting hole.

It takes $O(N^2)$ as time complexity to check cycle given a pair set. The overall time complexity for this implementation can be $O(N!N^2)$, which is still tolerable as $N \leq 12$.

# Code

## First version

The code to implement the idea is as following, this is the first version I worked out. 

{% gist 359f3ea08c26f7906b8528af57f8a2a2 %}

## Second version

After reading the solution, I think the idea is almost the same, but I have used several redundant variables which make the code look complicated. So I refactored the previous version, and the new code is as following.

{% gist 1b78fdd01c6fe4458bafcd2a01113a12 %}
