# 数组链表-双指针技巧解决链表题


## 1 合并两个有序链表  

### 题目

**力扣 [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)**

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

示例：

![img1](/img/labuladong/3-1.png)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

### 解析

这题比较简单，直接用 `while` 循环每次比较 `p1` 和 `p2` 的大小，把较小的节点接到结果链表上，如图所示：

![img1](/img/labuladong/3-2.png)

- 虚拟头节点技巧
  - 通过虚拟头节点这个占位符，可以避免处理空指针的情况，降低代码的复杂性。
  - 当你需要创造⼀条新链表的时候，可以使⽤虚拟头结点简化边界情况的处理。  
- 最后通过 `p.next = n1;` 将剩余的节点全都复制过去，不需要使用 `while` 循环一条一条复制。

代码实现：

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // 虚拟头结点
        ListNode p = new ListNode(), res = p;
        ListNode n1 = list1, n2 = list2;
        while(n1!=null && n2!=null){
            // ⽐较 n1 和 n2 两个指针
			// 将值较⼩的的节点接到 p 指针
            if(n1.val < n2.val){
                p.next = n1;  
                n1 = n1.next;
            } else {
                p.next = n2;
                n2 = n2.next;
            }
            // p 指针不断前进
            p = p.next;     
        }
		
        // 最后还剩的直接一次复制过去
        if(n1 != null){
            p.next = n1;
        }

        if(n2!=null){
            p.next = n2;
        }

        return res.next;
    }
}
```

## 2 单链表的分解  

### 题目

**力扣 [86. 分隔链表](https://leetcode.cn/problems/partition-list/)**

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你应当 保留 两个分区中每个节点的初始相对位置。

示例：

![img1](/img/labuladong/3-3.png)

```
输入：head = [1,4,3,2,5,2], x = 3
输出：[1,2,2,4,3,5]
```

### 解析

把原链表分成两个小链表，⼀个链表中的元素大小都小于 x，另⼀个链表中的元素都大于等于 x，最后再把这两条链表接到⼀起，就得到了题目想要的结果。  

❗❗❗注意：最后合并时，要先断开 bp 的 next 指针。因为 bp 是直接由 hp 复制过来的，后面可能还接着小于 x 的节点，因此可能在答案链表中形成循环。

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
    	// 存放大于 x 的链表的虚拟头节点
        ListNode big = new ListNode(), bp = big;   
        // 存放小于 x 的链表的虚拟头节点
        ListNode res = new ListNode(), rp = res;    
        ListNode hp = head; // 用于遍历head
        // 将一个链表分解成2个
        while(hp != null){
            if(hp.val < x){
                rp.next = hp;   // 利用虚拟节点
                rp = rp.next;
            } else{
                bp.next = hp;   // 利用虚拟节点
                bp = bp.next;
            }

            hp = hp.next;
        }

        bp.next = null;	// 断开bp的next指针
        rp.next = big.next;	// 合并2个链表

        return res.next;
    }
}
```

## 3 合并 k 个有序链表  

### 题目

**力扣 [23. 合并K个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)**

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

示例 ：

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

### 解析

合并 k 个有序链表的逻辑类似合并两个有序链表，难点在于，如何快速得到 k 个节点中的最小节点，接到结果链表上？  

这里我们就要用到 优先级队列（二叉堆） 这种数据结构，把链表节点放入⼀个最小堆，就可以每次获得 k 个节点中的最小节点：  

```java
ListNode mergeKLists(ListNode[] lists) {
	if (lists.length == 0) return null;
	// 虚拟头结点
	ListNode dummy = new ListNode(-1);
	ListNode p = dummy;
	// 优先级队列，最⼩堆
	PriorityQueue<ListNode> pq = new PriorityQueue<>(
		lists.length, (a, b)->(a.val - b.val));
	// 将 k 个链表的头结点加⼊最⼩堆
	for (ListNode head : lists) {
		if (head != null)
		pq.add(head);
	}
	while (!pq.isEmpty()) {
		// 获取最⼩节点，接到结果链表中
		ListNode node = pq.poll();
		p.next = node;
		if (node.next != null) {
			pq.add(node.next);
		}
		// p 指针不断前进
		p = p.next;
	}
	return dummy.next;
}
```

优先队列 pq 中的元素个数最多是 k，所以⼀次 poll 或者 add 方法的时间复杂度是 O(logk)；所有的链表节点都会被加入和弹出 pq，所以算法整体的时间复杂度是 O(Nlogk)，其中 k 是链表的条数，N 是这些链表的节点总数。  

## 4 单链表的倒数第 k 个节点  

### 题目1

寻找从后往前数的第 k 个节点。

### 解析1

##### 一般做法：遍历 2 次链表

假设链表有 n 个节点，倒数第 k 个节点就是正数第 n - k + 1 个节点。由于算法题⼀般只给⼀个 ListNode 头结点代表⼀条单链表，所以不能直接得出这条链表的长度 n，需要先遍历⼀遍链表算出 n 的值，然后再遍历链表计算第 n - k + 1 个节点。也就是说，这个解法需要**遍历两次链表**才能得到出倒数第 k 个节点。

##### 改进做法：遍历 1 次链表

首先，我们先让⼀个指针 p1 指向链表的头节点 head，然后走 k 步；再用⼀个指针 p2 指向链表头节点 head  ，则 p1 和 p2 之间相差 k 步。当 p1 走到末尾空指针时，p2 刚好在倒数第 k 个的位置。如图所示：

![img1](/img/labuladong/3-4.png)

代码实现如下：

```java
// 返回链表的倒数第 k 个节点
ListNode findFromEnd(ListNode head, int k) {
	ListNode p1 = head;
	// p1 先⾛ k 步
	for (int i = 0; i < k; i++) {
		p1 = p1.next;
	}
	ListNode p2 = head;
	// p1 和 p2 同时⾛ n - k 步
	while (p1 != null) {
		p2 = p2.next;
		p1 = p1.next;
	}
	// p2 现在指向第 n - k + 1 个节点，即倒数第 k 个节点
	return p2;
}
```

### 题目2

**力扣 [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)**

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

示例：

![img1](/img/labuladong/3-5.png)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

### 解析2

和上面思路类似，获得倒数第 n + 1 个节点的引用并进行删除。注意使用虚节点技巧，防止出现空指针的情况。代码如下：

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode p1 = new ListNode();	// 虚拟头结点
        p1.next = head;
        // 删除倒数第 n 个，要先找到倒数第 n+1 个节点
        ListNode p2 = findFromEnd(p1, n+1);
        p2.next = p2.next.next;
        return p1.next;
    }

    public ListNode findFromEnd(ListNode head, int n){
        ListNode p1 = head;
        ListNode p2 = head;
        for(int i=0; i<n; i++){
            p1 = p1.next;
        }
        while(p1 != null){
            p1 = p1.next;
            p2 = p2.next;
        }
        return p2;
    }
}
```

# 5 单链表的中点  

### 题目

**力扣 [876. 链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)**

给定一个头结点为 `head` 的非空单链表，返回链表的中间结点。如果有两个中间结点，则返回第二个中间结点。

示例 1：

```
输入：[1,2,3,4,5]
输出：此列表中的结点 3 (序列化形式：[3,4,5])
返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
注意，我们返回了一个 ListNode 类型的对象 ans，这样：
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
```

### 解析

##### 一般做法：遍历 2 次链表

需要遍历一次链表计算长度 n ，再遍历⼀次得到第 n / 2 个节点，也就是中间节点。  

##### 改进做法：遍历 1 次链表

使用【快慢指针】的技巧。我们让两个指针 slow 和 fast 分别指向链表头结点 head。每当慢指针 slow 前进⼀步，快指针 fast 就前进两步，这样，当 fast ⾛到链表末尾时，slow 就指向了链表中点。 代码实现如下：

```java
class Solution {
    ListNode middleNode(ListNode head) {
		// 快慢指针初始化指向 head
		ListNode slow = head, fast = head;
		// 快指针⾛到末尾时停⽌
		while (fast != null && fast.next != null) {
			// 慢指针⾛⼀步，快指针⾛两步
			slow = slow.next;
			fast = fast.next.next;
		}
	// 慢指针指向中点
	return slow;
	}
}
```

# 6 判断链表是否包含环  

### 解析

使用【快慢指针】。如果 fast 最终遇到空指针，说明链表中没有环；如果 fast 最终和 slow 相遇，那肯定是 fast 超过了 slow ⼀圈，说明链表中含有环。原理如图所示：

![img1](/img/labuladong/3-6.png)

```java
boolean hasCycle(ListNode head) {
	// 快慢指针初始化指向 head
	ListNode slow = head, fast = head;
	// 快指针⾛到末尾时停⽌
	while (fast != null && fast.next != null) {
		// 慢指针⾛⼀步，快指针⾛两步
		slow = slow.next;
		fast = fast.next.next;
		// 快慢指针相遇，说明含有环
		if (slow == fast) {
			return true;
		}
	}
	// 不包含环
	return false;
}
```

### 进阶

如果链表中含有环，如何计算这个环的起点？  

当快慢指针相遇时，让其中任⼀个指针指向头节点，然后让它俩以相同速度前进，再次相遇时所
在的节点位置就是环开始的位置。  代码如下：

```java
ListNode detectCycle(ListNode head) {
	ListNode fast, slow;
	fast = slow = head;
	while (fast != null && fast.next != null) {
		fast = fast.next.next;
		slow = slow.next;
		if (fast == slow) break;
	}
	// 上⾯的代码类似 hasCycle 函数
	if (fast == null || fast.next == null) {
		// fast 遇到空指针说明没有环
		return null;
	}
	// 重新指向头结点
	slow = head;
	// 快慢指针同步前进，相交点就是环起点
	while (slow != fast) {
		fast = fast.next;
		slow = slow.next;
}
return slow;
}
```




















