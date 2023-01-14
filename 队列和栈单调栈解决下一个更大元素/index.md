# 【队列和栈】单调栈解决下一个更大元素


## 1 单调栈模板

### 题目

输入一个数组 `nums`，请你返回⼀个等长的结果数组，结果数组中对应索引存储着下一个更大元素，如果没有更大的元素，就存 `-1`。  

### 解析

🔴 把数组的元素想象成并列站立的人，元素大小想象成人的身高。这些人面对你站成一列。如果能够看到元素「2」，那么他后面可见的第一个人就是「2」的下⼀个更大元素，因为比「2」小的元素身高不够，都被「2」挡住了，第一个露出来的就是答案。  

🟡 for 循环要从后往前扫描元素，因为我们借助的是**栈**的结构，**倒着入栈，其实是正着出栈**。

🟢 while 循环是**把两个「高个子」元素之间的元素排除**，因为他们的存在没有意义，前面挡着个「更高」的元素，所以他们不可能被作为后续进来的元素的下一个更大元素了。  

🔵  **这个算法的复杂度只有 O(n)。**  总共有 n 个元素，每个元素都被 push 入栈了一次，而最多会被 pop一次，没有任何冗余操作。  

图解：

![img1](/img/labuladong/5-1.png)

代码实现：

```java
int[] nextGreaterElement(int[] nums) {
	int n = nums.length;
	// 存放答案的数组
	int[] res = new int[n];
	Stack<Integer> s = new Stack<>();
	// 倒着往栈⾥放
	for (int i = n - 1; i >= 0; i--) {
		// 判定个⼦⾼矮
		while (!s.isEmpty() && s.peek() <= nums[i]) {
			// 矮个起开，反正也被挡着了。。。
			s.pop();
		}
		// nums[i] 身后的更⼤元素
		res[i] = s.isEmpty() ? -1 : s.peek();
		s.push(nums[i]);
	}
	return res;
}
```

## 2 进阶——下一个更大元素

### 题目

**力扣 [496. 下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/)**

`nums1` 中数字 `x` 的 **下一个更大元素** 是指 `x` 在 `nums2` 中对应位置 **右侧** 的 **第一个** 比 `x` 大的元素。

给你两个 **没有重复元素** 的数组 `nums1` 和 `nums2` ，下标从 **0** 开始计数，其中`nums1` 是 `nums2` 的子集。

对于每个 `0 <= i < nums1.length` ，找出满足 `nums1[i] == nums2[j]` 的下标 `j` ，并且在 `nums2` 确定 `nums2[j]` 的 **下一个更大元素** 。如果不存在下一个更大元素，那么本次查询的答案是 `-1` 。

返回一个长度为 `nums1.length` 的数组 `ans` 作为答案，满足 `ans[i]` 是如上所述的 **下一个更大元素** 。

### 解析

🟠 和上一题类似，只需要先把 nums2 中每个元素的下一个更大元素算出来存到一个映射里，然后再让 nums1 中的元素去查表即可。

🟣 **使用 HashMap 映射，可以提高代码效率。**

代码实现：

```java
public int[] nextGreaterElement(int[] nums1, int[] nums2) {
	// 记录 nums2 中每个元素的下⼀个更⼤元素
	int[] greater=nextGreaterElement(nums2);
    // 转化成映射：元素 x -> x 的下⼀个最⼤元素
	HashMap<Integer, Integer> greaterMap=new HashMap<>();
	for(int i=0; i<nums2.length; i++){
		greaterMap.put(nums2[i], greater[i]);
	}
	// nums1 是 nums2 的⼦集，所以根据 greaterMap 可以得到结果
	int[] res=new int[nums1.length];
	for(int i=0; i<nums1.length; i++){
		res[i]=greaterMap.get(nums1[i]);
	}

    return res;
}
```

## 3 进阶——下一个更大元素索引

### 题目

**力扣 [739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)**

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

### 解析

🟡 **这个问题本质上也是找下一个更大元素**，只不过现在不是问你下一个更大元素的值是多少，而是问你当前元素距离下一个更大元素的索引距离而已。  

🟤 **把之前记录最大值的堆栈改为记录最大值索引的堆栈即可。** 或是使用两个堆栈，一个记录索引，一个记录最大值，不过此时代码效率会降低。

代码实现：

```java
public int[] dailyTemperatures(int[] temperatures) {
	int n=temperatures.length;
	int[] res=new int[n];
	// 这⾥放元素索引，⽽不是元素
    Stack<Integer> index=new Stack<>();
    /* 单调栈模板 */
	for(int i=n-1; i>=0; i--){
		while(!index.isEmpty() && temperatures[index.peek()]<=temperatures[i]){
			index.pop();
        }
		// 得到索引间距
        res[i] = index.isEmpty() ? 0 : index.peek()-i; 
        // 将索引⼊栈，⽽不是元素
        index.push(i);
    }
    return res;
}
```

## 4 进阶——处理环形数组

### 题目

**力扣 [503. 下一个更大元素 II](https://leetcode.cn/problems/next-greater-element-ii/)**

给定一个循环数组 `nums` （ `nums[nums.length - 1]` 的下一个元素是 `nums[0]` ），返回 *`nums` 中每个元素的 **下一个更大元素*** 。

数字 `x` 的 **下一个更大的元素** 是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 `-1` 。

### 解析

🟠 **我们一般通过 `%` 运算符求模（余数），来模拟环形特效。**

🟢 这道题的难点在于，比如输入是 [2,1,2,4,3]，对于最后一个元素 3，如何找到元素 4 作为下一个更大元素。

🔵 对于这种需求，**常用套路就是将数组长度翻倍**。

🟣 我们可以不用构造翻倍的新数组，而是**利用循环数组的技巧来模拟数组长度翻倍的效果**。  

图解：

![img1](/img/labuladong/5-2.png)

代码实现：

```java
public int[] nextGreaterElements(int[] nums) {
	int n=nums.length;
	int[] res=new int[n];
	Stack<Integer> s=new Stack<>();
	// 数组⻓度加倍模拟环形数组
	for(int i=2*n-1; i>=0; i--){
        // 索引 i 要求模，其他的和模板⼀样
		while(!s.isEmpty() && s.peek()<=nums[i%n]){
			s.pop();
		}

		res[i%n] = s.isEmpty() ? -1 : s.peek();
		s.push(nums[i%n]);
	}

	return res;
}
```



参考资料：

https://labuladong.github.io/algo/


