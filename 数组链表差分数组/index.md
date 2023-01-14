# 【数组链表】差分数组


**差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减。**  

## 1 原理

### 题目

给出⼀个数组 nums，要求给区间 nums[2..6] 全部加 1，再给 nums[3..9] 全部减3，再给 nums[0..4] 全部加 2......N步操作后问，最后 nums 数组的值是什么？  

### 解析

**常规思路：**

用for循环都给 nums[i...j] 加上 val ，时间复杂度为 O(N)。由于对 nums 频繁修改，效率很低。

**差分数组：**

对 nums 数组构造⼀个 diff 差分数组，diff[i] 就是 nums[i] 和 nums[i-1] 之差。原理如图：

![img1](/img/labuladong/2-1.png)

这样构造差分数组 diff，就可以快速进行区间增减的操作，如果你想对区间 nums[i..j] 的元素全部加
3，那么只需要让 diff[i] += 3，然后再让 diff[j+1] -= 3 即可。

只要花费 O(1) 的时间修改 diff 数组，就相当于给 nums 的整个区间做了修改。多次修改 diff，然后通过 diff 数组反推，即可得到 nums 修改后的结果。  

代码实现如下：

```java
// 差分数组⼯具类
class Difference {
	// 差分数组
	private int[] diff;
    
	/* 输⼊⼀个初始数组，区间操作将在这个数组上进⾏ */
	public Difference(int[] nums) {
		assert nums.length > 0;
		diff = new int[nums.length];
		// 根据初始数组构造差分数组
		diff[0] = nums[0];
		for (int i = 1; i < nums.length; i++) {
			diff[i] = nums[i] - nums[i - 1];
		}
	}
    
	/* 给闭区间 [i, j] 增加 val（可以是负数）*/
	public void increment(int i, int j, int val) {
		diff[i] += val;
		if (j + 1 < diff.length) {
			diff[j + 1] -= val;
		}
	}
    
	/* 返回结果数组 */
	public int[] result() {
		int[] res = new int[diff.length];
		// 根据差分数组构造结果数组
		res[0] = diff[0];
		for (int i = 1; i < diff.length; i++) {
			res[i] = res[i - 1] + diff[i];
		}
		return res;
	}
}
```

注意 increment ⽅法中的 if 语句：当 j+1 >= diff.length 时，说明是对 nums[i] 及以后的整个数组都进⾏修改，那么就不需要再给 diff 数组减 val 了。

## 2 延伸——区间加法

### 题目

**力扣 [307. 区间加法](https://leetcode.cn/problems/range-addition/)**

假设你有一个长度为 `n` 的数组，初始情况下所有数字均为 `0` ，你将会被给出 `k` 个更新的操作。

其中，每个操作会被表示为一个三元组：`[startIndex, endIndex, inc]`，你需要将子数组 `A[startIndex ... endIndex]`（包括 `startIndex` 和 `endIndex` ）增加 `inc`。

请返回 `k` 次操作后的数组。

示例：

```java
输入：
length = 5, update = [[1,3,2],[2,4,3],[0,2,-2]]
输出：
[-2,0,3,5,3]

解释：
初始状态：[0,0,0,0,0]
进行了操作[1,3,2]后的状态：[0,2,2,2,0]
进行了操作[2,4,3]后的状态：[0,2,5,5,3]
进行了操作[0,2,-2]后的状态：[-2,0,3,5,3]
```

### 解析

使用刚才的 Difference 类：

```java
int[] getModifiedArray(int length, int[][] updates) {
	// nums 初始化为全 0
	int[] nums = new int[length];
	// 构造差分解法
	Difference df = new Difference(nums);
	for (int[] update : updates) {
		int i = update[0];
		int j = update[1];
		int val = update[2];
		df.increment(i, j, val);
	}
	return df.result();
}
```

## 3 延伸——航班预订系统

### 题目

**力扣 [1109. 航班预订统计](https://leetcode.cn/problems/corporate-flight-bookings/)**

这里有 `n` 个航班，它们分别从 `1` 到 `n` 进行编号。

有一份航班预订表 `bookings` ，表中第 i 条预订记录 `bookings[i] = [firsti, lasti, seatsi]` 意味着在从 `firsti` 到 `lasti` （包含 `firsti` 和 `lasti` ）的 每个航班 上预订了 `seatsi` 个座位。

请你返回一个长度为 `n` 的数组 `answer`，里面的元素是每个航班预定的座位总数。

示例 ：

```java
输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
输出：[10,55,45,25,25]
解释：
航班编号        1   2   3   4   5
预订记录 1 ：   10  10
预订记录 2 ：       20  20
预订记录 3 ：       25  25  25  25
总座位数：      10  55  45  25  25
因此，answer = [10,55,45,25,25]
```

### 解析

题目相当于：输入一个长度为 n 的数组 nums，其中所有元素都是 0。再给你输⼊⼀个 bookings，里面是若干三元组 (i, j, k)，每个三元组的含义就是要求你给 nums 数组的闭区间 [i-1,j-1] 中所有元素都加上 k。请你返回最后的 nums 数组。

> PS：因为题⽬说的 n 是从 1 开始计数的，⽽数组索引从 0 开始，所以对于输⼊的三元组 (i, j,
> k)，数组区间应该对应 [i-1,j-1]。  

这是一道标准的差分数组，利用上面的思想很容易实现： 

```java
class Solution {
    private int[] diff;
    public int[] corpFlightBookings(int[][] bookings, int n) {
        // 构造差分数组
        diff = new int[n];
        for(int i=0; i<bookings.length; i++){
            increment(bookings[i][0],bookings[i][1],bookings[i][2],n);
        }

        return result(n);
    }

    public void increment(int first, int last, int seat, int n){
        diff[first-1] += seat;
        if(last-1 < n-1){
            diff[last] -= seat;
        }
    }

    public int[] result(int n){
        int[] res = new int[n];
        res[0] = diff[0];
        for(int i=1; i<n; i++){
            res[i] = diff[i]+res[i-1];
        }
        return res;
    }
}
```

## 4 延伸——拼车

### 题目

**力扣 [1094. 拼车](https://leetcode.cn/problems/car-pooling/)**

车上最初有 `capacity` 个空座位。车 只能 向一个方向行驶（也就是说，不允许掉头或改变方向）

给定整数 `capacity` 和一个数组 `trips` ,  `trip[i] = [numPassengersi, fromi, toi]` 表示第 `i` 次旅行有 `numPassengersi` 乘客，接他们和放他们的位置分别是 `fromi` 和 `toi` 。这些位置是从汽车的初始位置向东的公里数。

当且仅当你可以在所有给定的行程中接送所有乘客时，返回 `true`，否则请返回 `false`。

示例 ：

```java
输入：trips = [[2,1,5],[3,3,7]], capacity = 4
输出：false
```

### 解析

旅客的上车和下车就相当于数组的区间加减；只要结果数组中的元素都小于 capacity，就说明可以不超载运输所有旅客。  

题目转化为差分数组，差分数组的长度即车站区间的个数，为1001。result 数组的值即为每段路程车上的人数。图解如下：

![img1](/img/labuladong/2-2.png)

代码实现：

```java
class Solution {

    private int[] diff;
    private int size = 1005;
    public boolean carPooling(int[][] trips, int capacity) {
        
        diff = new int[size];
        for(int i=0; i<trips.length; i++){
            increment(trips[i][1],trips[i][2]-1,trips[i][0]);
        }

        return result(capacity);
    }

    public void increment(int from, int to, int val){
        diff[from] += val;
        if(to < 1000){
            diff[to+1] -= val;
        }
    }

    public boolean result(int capacity){
        int[] result = new int[size];
        result[0] = diff[0];
        if(result[0] > capacity)
            return false;
        // 客⻋⾃始⾄终都不应该超载
        for(int i=1; i<=1000; i++){
            result[i] = result[i-1] + diff[i];
            if(result[i] > capacity){
                return false;
            }
        }
        return true;

    }
}
```



参考资料：

[https://labuladong.github.io/algo/](https://labuladong.github.io/algo/)

