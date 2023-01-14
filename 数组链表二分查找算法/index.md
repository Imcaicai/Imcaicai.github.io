# 【数组链表】二分查找算法


二分思维的精髓就是：**通过已知信息尽可能多地收缩搜索空间**，从而增加穷举效率，快速找到目标。  

## 1 二分查找框架

```java
int binarySearch(int[] nums, int target) {
	int left = 0, right = ...;
	while(...) {
		int mid = left + (right - left) / 2;
		if (nums[mid] == target) {
			...
		} else if (nums[mid] < target) {
			left = ...
		} else if (nums[mid] > target) {
			right = ...
		}
	}
	return ...;
}
```

计算 mid 时需要防止溢出，代码中 left + (right - left) / 2 就和 (left + right) / 2 的结果相同，但是有效防止了 left 和 right 太大，直接相加导致溢出的情况。  

## 2 寻找一个数

### 题目

**力扣 [704. 二分查找](https://leetcode.cn/problems/binary-search/)**

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。

### 解析

```java
int binarySearch(int[] nums, int target) {
	int left = 0;
	int right = nums.length - 1; // 注意
	while(left <= right) {
		int mid = left + (right - left) / 2;
		if(nums[mid] == target)
			return mid;
		else if (nums[mid] < target)
			left = mid + 1; // 注意
		else if (nums[mid] > target)
			right = mid - 1; // 注意
	}
	return -1;
}
```

🟠 **while 循环的条件中是 <=，而不是 <**

因为初始化 right 的赋值是 nums.length - 1，而不是 nums.length。前者相当于闭区间 [left, right]，后者相当于左闭右开区间 [left, right)，因为索引大小为 nums.length 是越界的。  

而只有搜索区间为空时才停止寻找，即 left <= right 时都应该继续循环。

🟡 **算法缺陷**

不能给出左侧 / 右侧边界。例如有序数组 nums = [1,2,2,2,3]，target 为 2，左侧索引为 1，右侧索引为 3 。按照该算法只能求出索引 2。

## 3 寻找左侧边界的二分搜索  

```java
int left_bound(int[] nums, int target) {
	int left = 0, right = nums.length - 1;
	// 搜索区间为 [left, right]
	while (left <= right) {
		int mid = left + (right - left) / 2;
		if (nums[mid] < target) {
			// 搜索区间变为 [mid+1, right]
			left = mid + 1;
		} else if (nums[mid] > target) {
			// 搜索区间变为 [left, mid-1]
			right = mid - 1;
		} else if (nums[mid] == target) {
			// 收缩右侧边界
			right = mid - 1;
		}
	}
	// 判断 target 是否存在于 nums 中
	// 此时 target ⽐所有数都⼤，返回 -1
	if (left == nums.length) return -1;
	// 判断⼀下 nums[left] 是不是 target
	return nums[left] == target ? left : -1;
}
```

## 4 寻找右侧边界的二分查找  

```java
int right_bound(int[] nums, int target) {
	int left = 0, right = nums.length - 1;
	while (left <= right) {
		int mid = left + (right - left) / 2;
		if (nums[mid] < target) {
			left = mid + 1;
		} else if (nums[mid] > target) {
			right = mid - 1;
		} else if (nums[mid] == target) {
			// 这⾥改成收缩左侧边界即可
			left = mid + 1;
		}
	}
	// 最后改成返回 left - 1
	if (left - 1 < 0) return -1;
	return nums[left - 1] == target ? (left - 1) : -1;
}
```

## 5 综合

### 题目

**力扣 [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)**

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

### 解析

综合使用上面的左侧二分查找、右侧二分查找即可。代码实现如下：（左侧二分查找和右侧二分查找代码省略，上面已有）

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int index1=left_bound(nums,target);
        int index2=right_bound(nums,target);
        return new int[] {index1,index2};
    }
}
```

以上就是二分查找算法的细节。在写二分查找代码时，注意以下 4 点：

🟢 分析二分查找代码时，不要出现 else，全部展开成 else if 方便理解。  

🔵 注意「搜索区间」和 while 的终止条件，如果存在漏掉的元素，记得在最后检查。

🟣 如需定义左闭右开的「搜索区间」搜索左右边界，只要在 nums[mid] == target 时做修改即可，搜索右侧时需要减一。

🟤 如果将「搜索区间」全都统一成两端都闭，好记，只要稍改 nums[mid] == target 条件处的代码和返回的逻辑即可。  

参考资料：

https://labuladong.github.io/algo/








