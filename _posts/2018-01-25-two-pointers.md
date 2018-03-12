---
layout: post
title:  "双指针"
categories: LeetCode
tags:  LeetCode TwoPointer
author: sunlei
---

* content
{:toc}


## 头尾指针

最经典的就是2Sum，一个low指针，一个high指针，找到序列中的两个数加起等于target。





~~~python
def two_sum(nums, target):
	low, high = 0, len(nums) - 1
	while low < high:
		x = nums[low] + nums[high]
		if x < target:
			low += 1
		elif x > target:
			high -= 1
		else:
			return True
	return False
~~~

头尾指针的变种有：

1. 满足累加和>=target的最长子序列，即 $$max(t-s+1) s.t. \Sigma^{i=t}_{i=s}nums[i]>=target$$,使用头尾两个指针。

2. Sort Colors: 三个color，分别0，1，2表示，给它排序。也是头尾指针，在forward的步骤上有区别，遇到0，与头指针交换，遇到1，continue，遇到2，与尾指针交换。


## 其他双指针

除了头尾指针都向中间走以外，还有两个头部指针，分别往前走。比如判断序列s是否为序列t的一个子序列。

其他双指针中较为特殊的一个是**快慢指针，主要用来判断是否有环。**

## 多指针

一个比较典型的例子就是[Ugly Number](https://leetcode.com/problems/ugly-number-ii/description/)
