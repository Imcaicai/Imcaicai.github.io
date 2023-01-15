# 【队列和栈】单调队列解决滑动窗口问题


【单调队列】主要是为了解决以下场景：

> 给你一个数组 window，已知其最值为 A，如果给 window 中添加⼀个数 B，那么比较一下 A 和 B 就可以立即算出新的最值；但如果要从 window 数组中减少一个数，就不能直接得到最值了，因为如果减少的这个数恰好是 A，就需要遍历 window 中的所有元素重新寻找新的最值。  

可以使用 **优先级队列** 来动态寻找最值，通过创建一个大（小）顶堆，可以很快拿到最大（小）值。

但优先级队列无法满足标准队列结构【先进先出】的时间顺序，因为优先级队列底层利用二叉堆对元素进行动态排序，元素的出队顺序是元素的大小顺序，和入队的先后顺序完全没有关系。  

而【单调队列】结构，既能够维护队列元素【先进先出】的时间顺序，又能够正确维护队列中所有元素的最值。  

## 1 题目

**力扣 [239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)**

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回 *滑动窗口中的最大值* 。

## 2 解析

### 2.1 搭建解题框架

🟧 普通队列的标准 API：

```java
class Queue{
	// enqueue 操作，在队尾加⼊元素 n
	void push(int n);
	// dequeue 操作，删除队头元素
	void pop();
}
```

🟨 【单调队列】的 API：

```java
class MonotonicQueue{
	// 在队尾添加元素 n
	void push(int n);
	// 返回当前队列中的最⼤值
	int max();
	// 队头元素如果是 n，删除它
	void pop(int n);
}
```

🟩 【滑动窗口】问题的解答框架  

```java
int[] maxSlidingWindow(int[] nums, int k) {
	MonotonicQueue window = new MonotonicQueue();
	List<Integer> res = new ArrayList<>();
	for (int i = 0; i < nums.length; i++) {
		if (i < k - 1) {
			//先把窗⼝的前 k - 1 填满
			window.push(nums[i]);
		} else {
			// 窗⼝开始向前滑动
			// 移⼊新元素
			window.push(nums[i]);
			// 将当前窗⼝中的最⼤元素记⼊结果
			res.add(window.max());
			// 移出最后的元素
			window.pop(nums[i - k + 1]);
		}
	}
	// 将 List 类型转化成 int[] 数组作为返回值
	int[] arr = new int[res.size()];
	for (int i = 0; i < res.size(); i++) {
		arr[i] = res.get(i);
	}
	return arr;
}
```

### 2.2 实现单调队列数据结构  

🟠 实现「单调队列」必须使用一种数据结构支持在头部和尾部进行插入和删
除，很明显双链表满足这个条件。  

🟡 由于只输出每个窗口内的最大元素，所以实现 push 方法时依然在队尾添加元素，但要把前面比自己小的元素都删掉。

```java
class MonotonicQueue {
	// 双链表，⽀持头部和尾部增删元素
	// 维护其中的元素⾃尾部到头部单调递增
	private LinkedList<Integer> maxq = new LinkedList<>();
	// 在尾部添加⼀个元素 n，维护 maxq 的单调性质
	public void push(int n) {
		// 将前⾯⼩于⾃⼰的元素都删除
		while (!maxq.isEmpty() && maxq.getLast() < n) {
			maxq.pollLast();
		}
		maxq.addLast(n);
	}
}
```

🟢 最终单调队列的元素会保持 **单调递减** 的顺序，因此 max 方法只用返回队首元素：

```java
public int max() {
	// 队头的元素肯定是最⼤的
	return maxq.getFirst();
}
```

🔵 pop 方法在队头删除元素 n  ：

```java
public void pop(int n) {
	if (n == maxq.getFirst()) {
		maxq.pollFirst();
	}
}
```

完整代码：

```java
/* 单调队列的实现 */
class MonotonicQueue {
	LinkedList<Integer> maxq = new LinkedList<>();
	public void push(int n) {
		// 将⼩于 n 的元素全部删除
		while (!maxq.isEmpty() && maxq.getLast() < n) {
			maxq.pollLast();
		}
		// 然后将 n 加⼊尾部
		maxq.addLast(n);
	}
	public int max() {
		return maxq.getFirst();
	}
	public void pop(int n) {
		if (n == maxq.getFirst()) {
			maxq.pollFirst();
		}
	}
}

/* 解题函数的实现 */
int[] maxSlidingWindow(int[] nums, int k) {
	MonotonicQueue window = new MonotonicQueue();
	List<Integer> res = new ArrayList<>();
	for (int i = 0; i < nums.length; i++) {
		if (i < k - 1) {
			//先填满窗⼝的前 k - 1
			window.push(nums[i]);
		} else {
			// 窗⼝向前滑动，加⼊新数字
			window.push(nums[i]);
			// 记录当前窗⼝的最⼤值
			res.add(window.max());
			// 移出旧数字
			window.pop(nums[i - k + 1]);
		}
	}
	// 需要转成 int[] 数组再返回
	int[] arr = new int[res.size()];
	for (int i = 0; i < res.size(); i++) {
		arr[i] = res.get(i);
	}
	return arr;
}
```

🟣 nums 中的每个元素最多被 push 和 pop ⼀次，没有任何多余操作，所以整体的复杂度是 **O(N)**。空间复杂度就是窗口的大小 **O(k)**。  

参考资料：

https://labuladong.github.io/algo/






