# 算法和刷题的框架思维


## 1 数据结构的存储方式

### 1.1 数据结构&存储方式

数据的存储方式只有两种：数组（顺序存储）和链表（链式存储）。

【队列】【栈】

【图】：链表实现就是邻接表，二维数组实现就是邻接矩阵

【散列表】

【树】：数组实现就是堆（完全二叉树），链表实现就是普通二叉树

### 1.2 数组&链表优缺点

数组：

- 紧凑连续存储,可以随机访问，通过索引快速找到对应元素，节约存储空间。
- 内存空间必须⼀次性分配够，扩容时需要重新分配空间，再把数据全部复制过去，时间复杂度 O(N)
- 插⼊和删除时间复杂度 O(N)  

链表：

- 元素不连续，不存在数组的扩容问题；
- 如果知道某⼀元素的前驱和后驱，插入删除时间复杂度 O(1)
- 存储空间不连续，不能随机访问
- 每个元素必须存储指向前后元素位置的指针，会消耗相对更多的储存空间。  

## 2 数据结构的基本操作

数据结构的基本操作：遍历+访问（增删查改），分为线性/非线性。

线性即for/while迭代，非线性即递归。

🟡 **【数组遍历框架】 迭代**

```c++
void traverse(int[] arr) {
	for (int i = 0; i < arr.length; i++) {
	// 迭代访问 arr[i]
	}
}
```

🟢 **【链表遍历框架】 迭代/递归**

```c++
/* 基本的单链表节点 */
class ListNode {
	int val;
	ListNode next;
}

void traverse(ListNode head) {
	for (ListNode p = head; p != null; p = p.next) {
		// 迭代访问 p.val
	}
}

void traverse(ListNode head) {
    // 递归访问 head.val
	traverse(head.next);
}
```

🔵 **【二叉树遍历框架】 递归**

```c++
/* 基本的⼆叉树节点 */
class TreeNode {
	int val;
	TreeNode left, right;
}

void traverse(TreeNode root) {
	traverse(root.left);
	traverse(root.right);
}
```



🟣 **【N叉树遍历框架】 递归**

```c++
/* 基本的 N 叉树节点 */
class TreeNode {
	int val;
	TreeNode[] children;
}

void traverse(TreeNode root) {
	for (TreeNode child : root.children)
		traverse(child);
}
```

N 叉树的遍历又可以扩展为图的遍历，因为图就是好几个 N 叉棵树的结合体。图是可能出现环的，用个布尔数组 visited 做标记就行。  

## 3 算法刷题指南

✅ **先学习像数组、链表这种基本数据结构的常用算法，比如单链表翻转，前缀和数组，二分搜索等。**  因为这些算法属于会者不难难者不会的类型，难度不大，学习它们不会花费太多时间。而且这些小而美的算法经常让你大呼精妙，能够有效培养你对算法的兴趣。  

✅ 学会基础算法之后，不要急着上来就刷回溯算法、动态规划这类笔试常考题，而应该**先刷⼆叉树**。因为⼆叉树是最容易培养框架思维的，而且⼤部分算法技巧，本质上都是树的遍历问题。

✅ **刷题试着从框架上看问题，而不要纠结于细节问题。**  纠结细节问题，就比如纠结 i 到底应该加到 n 还是加到 n - 1，这个数组的⼤⼩到底应该开 n 还是 n + 1？  



参考资料：

[https://labuladong.github.io/algo/](https://labuladong.github.io/algo/)










