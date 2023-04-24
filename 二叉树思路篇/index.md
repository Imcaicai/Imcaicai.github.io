# 【二叉树】思路篇


> **【二叉树问题的通用思路】**
>
> 🔴 是否可以通过 **遍历一遍二叉树** 得到答案？如果可以，用一个 traverse 函数配合外部变量来实现。
>
> 🟡 是否可以定义一个 **递归函数** ，通过 **子问题（子树）** 的答案推导出原问题的答案？如果可以，写出这个递归函数的定义，并充分利用这个函数的 **返回值** 。
>
> 🔵 明白二叉树的 **每个节点需要做什么** ，需要 **在什么时候（前中后序）做** 。



### 1 翻转二叉树

#### 题目

🔗力扣 [226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

给你一棵二叉树的根节点 `root` ，翻转这棵二叉树，并返回其根节点。

![img1](/img/labuladong/8-1.png)

#### 遍历一遍二叉树

tips：遍历一遍二叉树一般比分解子问题快一些。

```c++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        traverse(root);
        return root;
    }

    TreeNode* traverse(TreeNode* root){
        if(root==nullptr)
            return nullptr;

        // 前序位置，交换左右子节点
        TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;

        traverse(root->left);
        traverse(root->right);
        return root;
    }
};
```

#### 分解问题

```c++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(root==nullptr)
            return nullptr;
        TreeNode* temp = invertTree(root->left);
        root->left = invertTree(root->right);
        root->right = temp;
        return root;
    }
};
```



### 2 填充节点的右侧指针

#### 题目

🔗力扣 [116. 填充每个节点的下一个右侧节点指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)

给定一个 **完美二叉树** ，其所有叶子节点都在同一层，每个父节点都有两个子节点。

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。

初始状态下，所有 next 指针都被设置为 `NULL`。

![img2](/img/labuladong/8-2.png)

#### 遍历一遍二叉树

![img3](/img/labuladong/8-3.png)

📌 使用传统的 traverse 函数是遍历二叉树的所有节点，但本题需要遍历的其实是 **两个相邻节点之间的【空隙】。**  

📌 所以我们将二叉树抽象成三叉树，三叉树上的每个节点就是原先二叉树的两个相邻节点。 

📌 只需实现一个 traverse 函数来遍历这棵三叉树，每个三叉树节点需要做的事就是把自己内部的两个二叉树节点穿起来。

```c++
class Solution {
public:
    Node* connect(Node* root) {
        if (root == NULL) return NULL;
        // 遍历三叉树相邻结点
        traverse(root->left,root->right);
        return root;
    }

    void traverse(Node* n1, Node* n2){
        if(n1==NULL || n2==NULL)
            return;
        
        // 前序位置
        // 将传入的两个结点串起来
        n1->next = n2;
        // 连接相同⽗节点的两个⼦节点
        traverse(n1->left, n1->right);	
        traverse(n2->left, n2->right);
        // 连接跨越⽗节点的两个⼦节点
        traverse(n1->right, n2->left); 
    }
};
```

#### 分解问题

这道题类似于层序遍历，无法用分解问题的思维解决。



### 3 将二叉树展开为链表

#### 题目

🔗力扣 [114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

给你二叉树的根结点 `root` ，请你将它展开为一个单链表：

- 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
- 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

![img4](/img/labuladong/8-4.png)

#### 遍历一遍二叉树

可以对整棵树进行前序遍历，一边遍历一边构造出一条链表。

但是 flatten 函数的返回类型为 void，也就是说题目希望我们在原地把二叉树拉平成链表。 简单的二叉树遍历无法解决。

```java
// 虚拟头节点，dummy.right 就是结果
TreeNode dummy = new TreeNode(-1);
// ⽤来构建链表的指针
TreeNode p = dummy;
void traverse(TreeNode root) {
	if (root == null) {
		return;
	}
	// 前序位置
	p.right = new TreeNode(root.val);
	p = p.right;
	traverse(root.left);
	traverse(root.right);
}
```

#### 分解问题

📌 先利用 flatten(x.left) 和 flatten(x.right) 将 x 的左右子树拉平。 

📌 将 x 的右子树接到左子树下方，然后将整个左子树作为右子树。

![img5](/img/labuladong/8-5.png)

```c++
class Solution {
public:
    void flatten(TreeNode* root) {
        if(root==nullptr)
            return;
        
        // 利⽤定义，把左右⼦树拉平
        flatten(root->left);
        flatten(root->right);

        // 后续遍历位置
        // 将左⼦树作为右⼦树
        TreeNode* left = root->left;
        TreeNode* right = root->right;
        root->left = nullptr;
        root->right = left;
		
        // 将原先的右⼦树接到当前右⼦树的末端
        TreeNode* p = root;
        while(p->right != nullptr)
            p = p->right;
        p->right = right;
    }
};
```


