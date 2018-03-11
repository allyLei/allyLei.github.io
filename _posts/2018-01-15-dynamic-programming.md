---
layout: post
title:  "动态规划"
categories: LeetCode
tags:  LeetCode
author: sunlei
---

* content
{:toc}

## 迷宫路径问题

最easy的就是给$$m*n$$迷宫，能够走的路径的个数，变种有：加了阻碍的路径个数，迷宫的具有最小和的路径。





其他一些较为经典的DP：
1. Edit distance问题
2. 走台阶问题， [decode ways](https://leetcode.com/problems/decode-ways/), [unique binary search trees](https://leetcode.com/problems/unique-binary-search-trees/description/)



## Longest Common SubSequence

LCS是一个经典的DP问题。


注意，它和最长公共子串不同，最长公共子串要求连续，这个使用一维数据DP就可以解决：
```
def findLength(A, B):
	ret = 0
	D = [0] * (len(B) + 1)
	for i in range(len(A)):
		for j in range(len(B)-1, -1, -1):
			if A[i] == B[j]:
				D[j+1] = D[j] + 1
				ret = max(D[j+1], ret)
			else:
				D[j+1] = 0
	return ret
```
