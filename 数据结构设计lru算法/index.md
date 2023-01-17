# 【数据结构设计】LRU算法


## 1 题目

**力扣 [146. LRU 缓存](https://leetcode.cn/problems/lru-cache/)**

请你设计并实现一个满足 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 约束的数据结构。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 **逐出** 最久未使用的关键字。

函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

## 2 解析

### 2.1 LRU算法设计

由于 put 和 get 方法的时间复杂度为 O(1)，我们可以总结出 cache 这个数据结构必要的条件：  

> 🔴 cache 中的元素必须有时序，以区分最近使用的和久未使用的数据  
>
> 🟡 能在 cache 中快速找某个 key 是否已存在并得到对应的 val  
>
> 🟢 cache 要支持在任意位置快速插入和删除元素，便于访问某个 key 并将这个元素变为最近使用的

哈希表查找快，但是数据无固定顺序；链表有顺序之分，插入删除快，但是查找慢。所以结合一下，形成一种新的数据结构：**哈希链表 LinkedHashMap**。  

LRU 缓存算法的核心数据结构就是**哈希链表**，**双向链表和哈希表的结合体**。 如图所示：

![img1](/img/labuladong/6-1.png)

根据这个结构分析以上 3 个条件：

> 🟥 每次从链表尾部添加元素，则尾部就是最近使用的，头部就是最久未使用的
>
> 🟨 通过哈希表可以根据 key 快速获得 val
>
> 🟩 用哈希表将 key 快速映射到链表节点后，可以很方便的进行插入、删除操作

两个问题：

> ❓ 用单链表取代双链表行不行？
>
> ❓ 哈希表中存了 key，链表中为什么还要存 key 和 val？可以只存 val 吗？



### 2.2 代码实现

首先写出双链表节点类型 Node：

```java
class Node {
	public int key, val;
	public Node next, prev;
	public Node(int k, int v) {
		this.key = k;
		this.val = v;
    }
}
```

再用 Node 类型构建一个双链表：

```java
class DoubleList {
	// 头尾虚节点
	private Node head, tail;
	// 链表元素数
	private int size;
	public DoubleList() {
		// 初始化双向链表的数据
		head = new Node(0, 0);
		tail = new Node(0, 0);
		head.next = tail;
		tail.prev = head;
		size = 0;
	}
	// 在链表尾部添加节点 x，时间 O(1)
	public void addLast(Node x) {
		x.prev = tail.prev;
		x.next = tail;
		tail.prev.next = x;
		tail.prev = x;
		size++;
	}
	// 删除链表中的 x 节点（x ⼀定存在）
	// 由于是双链表且给的是⽬标 Node 节点，时间 O(1)
	public void remove(Node x) {
		x.prev.next = x.next;
		x.next.prev = x.prev;
		size--;
	}
	// 删除链表中第⼀个节点，并返回该节点，时间 O(1)
	public Node removeFirst() {
		if (head.next == tail)
			return null;
		Node first = head.next;
		remove(first);
	return first;
	}
	// 返回链表⻓度，时间 O(1)
	public int size() { return size; }
}
```

 ✅ **【为什么必须要用双向链表】** 

因为我们需要删除操作。删除⼀个节点不光要得到该节点本身的指针，也需要操作其前驱节点的指针，而双向链表才能支持直接查找前驱，保证操作的时间复杂度 O(1)。  

有了双向链表的实现，我们只需要在 LRU 算法中把它和哈希表结合起来即可，先搭出代码框架：  

```java
class LRUCache {
	// key -> Node(key, val)
	private HashMap<Integer, Node> map;
	// Node(k1, v1) <-> Node(k2, v2)...
	private DoubleList cache;
	// 最⼤容量
	private int cap;
	public LRUCache(int capacity) {
        this.cap = capacity;
		map = new HashMap<>();
		cache = new DoubleList();
	}
}
```

一些辅助函数的实现：

```java
/* 将某个 key 提升为最近使⽤的 */
private void makeRecently(int key) {
	Node x = map.get(key);
	// 先从链表中删除这个节点
	cache.remove(x);
	// 重新插到队尾
	cache.addLast(x);
}
/* 添加最近使⽤的元素 */
private void addRecently(int key, int val) {
	Node x = new Node(key, val);
	// 链表尾部就是最近使⽤的元素
	cache.addLast(x);
	// 别忘了在 map 中添加 key 的映射
	map.put(key, x);
}
/* 删除某⼀个 key */
private void deleteKey(int key) {
	Node x = map.get(key);
	// 从链表中删除
	cache.remove(x);
	// 从 map 中删除
	map.remove(key);
}
/* 删除最久未使⽤的元素 */
private void removeLeastRecently() {
	// 链表头部的第⼀个元素就是最久未使⽤的
	Node deletedNode = cache.removeFirst();
	// 同时别忘了从 map 中删除它的 key
	int deletedKey = deletedNode.key;
	map.remove(deletedKey);
}
```

✅ **【为什么要在链表中同时存储 key 和 val，而不是只存储 val】** 

当缓存容量已满，我们不仅仅要删除最后⼀个 Node 节点，还要把 map 中映射到该节点的 key 也删除，而 key 只能由 Node 得到。如果 Node 结构中只存储 val，那么我们就无法得知 key 是什么，也就无法删除 map 中的键。  

根据以上辅助函数，可以实现 put 和 get 函数：

```java
public int get(int key) {
	if (!map.containsKey(key)) {
		return -1;
	}
	// 将该数据提升为最近使⽤的
	makeRecently(key);
	return map.get(key).val;
}

public void put(int key, int val) {
	if (map.containsKey(key)) {
		// 删除旧的数据
		deleteKey(key);
		// 新插⼊的数据为最近使⽤的数据
		addRecently(key, val);
		return;
	}
	if (cap == cache.size()) {
		// 删除最久未使⽤的元素
		removeLeastRecently();
	}
	// 添加为最近使⽤的元素
	addRecently(key, val);
}
```

## 3 内置类型 LinkedHashMap  

也可以用 Java 的内置类型 LinkedHashMap 来实现 LRU 算法，逻辑和之前完全⼀致：

```java
class LRUCache {
	int cap;
	LinkedHashMap<Integer, Integer> cache = new LinkedHashMap<>();
	public LRUCache(int capacity) {
		this.cap = capacity;
	}
    
	public int get(int key) {
		if (!cache.containsKey(key)) {
			return -1;
		}
		// 将 key 变为最近使⽤
		makeRecently(key);
		return cache.get(key);
	}
    
	public void put(int key, int val) {
		if (cache.containsKey(key)) {
			// 修改 key 的值
			cache.put(key, val);
			// 将 key 变为最近使⽤
			makeRecently(key);
			return;
		}
		if (cache.size() >= this.cap) {
			// 链表头部就是最久未使⽤的 key
			int oldestKey = cache.keySet().iterator().next();
			cache.remove(oldestKey);
		}
		// 将新的 key 添加链表尾部
		cache.put(key, val);
	}
    
	private void makeRecently(int key) {
		int val = cache.get(key);
		// 删除 key，重新插⼊到队尾
		cache.remove(key);
		cache.put(key, val);
	}
}
```



参考资料：

https://labuladong.github.io/algo/






