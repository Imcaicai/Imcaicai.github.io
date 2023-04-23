# 【二叉树】纲领篇


### 1 快速排序&归并排序

#### 🔴 快速排序：二叉树的前序遍历

**先构造分界点，然后去左右子数组构造分界点。** 要对 nums[lo..hi] 进行排序，我们先找⼀个分界点 p，通过交换元素使得 nums[lo..p-1] 都小于等于 nums[p]，且 nums[p+1..hi] 都大于 nums[p]，然后递归地去 nums[lo..p-1] 和 nums[p+1..hi] 中寻找新的分界点。

```java
void sort(int[] nums, int lo, int hi) {
	/****** 前序遍历位置 ******/
	// 通过交换元素构建分界点 p
	int p = partition(nums, lo, hi);
	
	sort(nums, lo, p - 1);
	sort(nums, p + 1, hi);
}
```

#### 🔵 归并排序：二叉树的后序遍历

**先对左右子数组排序，然后合并，即分治算法。** （对 nums[lo..hi] 进行排序，我们先对 nums[lo..mid] 排序，再对 nums[mid+1..hi] 排序，最后把这两个有序的子数组合并。

```java
void sort(int[] nums, int lo, int hi) {
	int mid = (lo + hi) / 2;
	sort(nums, lo, mid);
	sort(nums, mid + 1, hi);
    
	/****** 后序位置 ******/
 	// 合并 nums[lo..mid] 和 nums[mid+1..hi]
	merge(nums, lo, mid, hi);
}
```



### 2 深入理解前中后序

#### 🟡 一般的二叉树遍历框架

```java
void traverse(TreeNode root) {
	if (root == null) {
	return;
	}
	// 前序位置
	traverse(root.left);
	// 中序位置
	traverse(root.right);
	// 后序位置
}
```

#### 🟢 前中后序

|          | 前序                                   | 中序                                                     | 后序                                           |
| -------- | -------------------------------------- | -------------------------------------------------------- | ---------------------------------------------- |
| 执行时间 | 刚进入一个节点的时候                   | 在⼀个二叉树节点左子树都遍历完，即将开始遍历右子树的时候 | 即将离开一个节点的时候                         |
| 获取数据 | 只能从函数参数中获取父节点传递来的数据 | 当前节点的数据                                           | 参数数据；<br>子树通过函数返回值传递回来的数据 |

- 每个节点都有【唯一】属于自己的前中后序位置
- 前中后序是遍历二叉树过程中处理每一个节点的三个特殊时间点
- 二叉树的所有问题，就是在前中后序位置注入代码逻辑，去达到自己的目的，你只需要单独思考【每一个节点应该做什么】

![img1](/img/labuladong/7-1.png)



### 3 两种解题思路

#### 🟠 两种解题思路

- **【遍历一遍二叉树】对应回溯算法、快速排序**
- **【分解问题】 对应动态规划、归并排序**

#### 🔵 例题

**题目**

【力扣】[104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**遍历一遍二叉树**

```c++
class Solution {
public:
    int res=0;  // 最大深度
    int depth=0;    // 当前节点的深度
public:
    int maxDepth(TreeNode* root) {
        traverse(root);
        return res;
    }

    void traverse(TreeNode* root){
        if(root==nullptr)
            return;
        
        depth++;
        traverse(root->left);
        traverse(root->right);
        res=max(res,depth);
        depth--;
    }
};
```

**分解问题**

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root==nullptr)
            return 0;
        int left = maxDepth(root->left);
        int right = maxDepth(root->right);
        return max(left,right)+1;
    }
};
```

#### 🟡 二叉树问题的通用思路

- 是否可以通过 **遍历一遍二叉树** 得到答案？如果可以，用一个 traverse 函数配合外部变量来实现。
- 是否可以定义一个 **递归函数** ，通过 **子问题（子树）** 的答案推导出原问题的答案？如果可以，写出这个递归函数的定义，并充分利用这个函数的 **返回值** 。
- 明白二叉树的 **每个节点需要做什么** ，需要 **在什么时候（前中后序）做** 。

