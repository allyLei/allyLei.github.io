---
layout: post
title:  "二分查找"
categories: LeetCode
tags:  LeetCode
author: sunlei
---

* content
{:toc}


二分查找的基本思路都是，通过不断缩小查找范围，达到logn的时间复杂度。




## Binary Search

经典的Binary Search查找target如下：
```
low, high = 0, len(nums)
while low < high:
	i = (low + high) // 2
	if nums[i] < target:
		low = i + 1
	elif nums[i] > target:
		high = i
	else:
		return i
```

Binary search的简单的变种有，在有序数组中search范围（左范围+右范围），在某个点平移有序数组后的数组中查找target等等。

应用binary search的题目有：
 1. 两个sorted array， log(m+n)时间内得到中位数
 2. 查找重复数字, 左边or右边
 3. 一些数学运算如 sqrt(x), pow(x, n), divide(x, y)等


## Quick Sort
Quick sort也是分治法
```
def quick_sort(nums, start, end):
	if start >= end:
		return
	low, high, pivot = start, end, nums[start]
	while low < high:
		while low < high and nums[high] > pivot:
			high -= 1
		while low < high and nums[low] <= pivot:
			low += 1
		nums[low], nums[high] = nums[high], nums[low]
	nums[start], nums[low] = nums[low], pivot
	quick_sort(nums, start, low-1)
	quick_sort(nums, low+1, end)
```
