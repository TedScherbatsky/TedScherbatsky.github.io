---
title: 利用多指针进行元素移动
date: 2021-04-02 00:36:29
tags:
- 算法
---

## 一、插入排序

以下两种思路不同，但实现几乎一样。

### 思路1 每轮寻找合适位置插入

```Python
def insert_sort1(nums):
    for i in range(1, len(nums)):
        cur = nums[i]  # 把当前第i个元素插入前i-1的列表中，最终前i个数有序
        # 从i前一个数开始，和cur比较，更大者则往右移动
        j = i-1  
        while j >= 0 and nums[j] > cur:  # 如果比当前数大
            nums[j+1] = nums[j]  # 往右挪一位
            j -= 1
        # 此时j为-1,cur插入0这个位置 , 或者nums[j]比当前数小,插入j+1这个位置
        nums[j+1] = cur
```

### 思路2 每轮给位置找合适的数

```Python
def insert_sort2(nums):
    for i in range(1, len(nums)):
        cur = nums[i]  # 把当前第i个元素插入前i-1的列表中，最终前i个数有序
        # 从i开始，给每个位置找正确的数
        j = i  
        while j-1 >= 0 and nums[j-1] > cur:  # j-1比cur大
            nums[j] = nums[j-1]  # j这个位置让给nums[j-1]坐
            j -= 1
        nums[j] = cur # j-1没有数了，或者cur比nums[j-1]大，总之j这个位置让给cur坐
```

## 二、移动0问题

LeetCode [283. Move Zeroes](https://leetcode-cn.com/problems/move-zeroes/):  
给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。如
**输入:** `[0,1,0,3,12]`
**输出:** `[1,3,12,0,0]`
以下两种思路不同而算法复杂度不同。

### 思路1 把1插入到前边

循环每一个元素，判断它左边的所有元素是不是为1，如果有不是1的（也就是有0），那就把0往右移1个位置，把当前的元素赋到0这个位置。
时间复杂度：O(n^2)

```Python
def moveZeroes(nums):
    for i in range(1,len(nums)):
        # if nums[i] == 0: continue # 小优化
    	j = i-1
    	while j >= 0 and nums[j] == 0:
    		nums[j+1], nums[j] = nums[j], nums[j+1] 
    		j-=1
```

### 思路2 给位置找到合适的数

给index位置的找正确的数，如果i非0，则放入，并且index前进；否则i进行前进。
时间复杂度：O(n)

```Python
def moveZeroes(nums):
    index = 0
    for i in range(len(nums)):
        if nums[i] != 0:
            nums[index] = nums[i]
            index+=1
    while index < len(nums):
        nums[index] = 0
        index+=1
```

## 三、快速排序中的划分

输入：一个数组`nums`，和需要换分的起`start`止`end`位置。
输出：返回划分元素x的下标i，数组已经被整理，i左边的数小于等于x，i右边的数大于x。
以下两种思路，实现难度是不同的。

### 思路1 不断左右交换

从左右两边开始不断『寻找大于x和小于等于x的数，进行交换』，左右相遇。

```Python
def partition(nums, start, end):
	if start == end: return end
	i = start
	j = end
	x = nums[i]
	while i < j:
		while i<j and nums[j]>x: # 找到右边小于等于x的数
			j-=1
		if i<j:
			nums[i] = nums[j] # nums[j]去到了i这个位置,j这个位置的数可以被覆盖了
			i+=1
		while i<j and nums[i]<x: # 找到左边大于等于x的数
			i+=1
		if i<j:
			nums[j] = nums[i]  # nums[i]去到了j这个位置,i这个位置的数可以被覆盖了
			j-=1
	nums[i]=x # 最后覆盖i这个位置
	return i
```

### 思路2 给位置找到合适的数

从左边开始，给位置找到合适的数.

```Python
def partition(nums, start, end):
    if start == end: return end
    x = nums[end]
    index = start
    for i in range(start,end): # 最后一个不用
        if nums[i] <= x:
            nums[i], nums[index] = nums[index], nums[i]
            index+=1
    nums[index], nums[end] = nums[end],nums[index] # 这里处理最后一个
    return index
```