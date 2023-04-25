# 【二叉树】构造篇


> **【二叉树问题的通用思路】**
>
> 🔴 是否可以通过 **遍历一遍二叉树** 得到答案？如果可以，用一个 traverse 函数配合外部变量来实现。
>
> 🟡 是否可以定义一个 **递归函数** ，通过 **子问题（子树）** 的答案推导出原问题的答案？如果可以，写出这个递归函数的定义，并充分利用这个函数的 **返回值** 。
>
> 🔵 明白二叉树的 **每个节点需要做什么** ，需要 **在什么时候（前中后序）做** 。
>
> 🟢 **二叉树的构造问题⼀般使用【分解问题】的思路：构造整棵树 = 根节点 + 构造左子树 + 构造右子树。**  



### 1 最大二叉树

#### 题目

🔗力扣 [654. 最大二叉树](https://leetcode.cn/problems/maximum-binary-tree/)

给定一个不重复的整数数组 `nums` 。 **最大二叉树** 可以用下面的算法从 `nums` 递归地构建:

1. 创建一个根节点，其值为 `nums` 中的最大值。
2. 递归地在最大值 **左边** 的 **子数组前缀上** 构建左子树。
3. 递归地在最大值 **右边** 的 **子数组后缀上** 构建右子树。

返回 *`nums` 构建的* ***最大二叉树*** 。

#### 解析

🔴 遍历数组把找到最大值 maxVal，得出根节点 root 。

🔵 对 maxVal 左边的数组和右边的数组进行递归构建，作为 root 的左右子树。  

```c++
class Solution {
public:
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        return build(nums, 0, nums.size()-1);
    }

    TreeNode* build(vector<int>& nums, int l, int r){
        // base case
        if(l>r) return nullptr;

        // 寻找最大的值
        int max=-1,idx=-1;
        for(int i=l;i<=r;i++){
            if(nums[i]>max){
                max = nums[i];idx = i;
            }
        }

        // 构造根节点
        TreeNode* root = new TreeNode(max);

        // 构造左右子节点
        root->left = build(nums, l, idx-1);
        root->right = build(nums, idx+1, r);
        return root;
    }
};
```



### 2 从前序与中序遍历序列构造二叉树

#### 题目

🔗力扣 [105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

给定两个整数数组 `preorder` 和 `inorder` ，其中 `preorder` 是二叉树的**先序遍历**，`inorder` 是同一棵树的**中序遍历**，请构造二叉树并返回其根节点。

#### 解析

🟡 类似上一题，想办法得出根节点，然后递归构造左右子树。  

🟢 回顾前中后序遍历框架

```c++
// 前序遍历
void traverse(TreeNode root) {
	preorder.add(root.val);
	traverse(root.left);
	traverse(root.right);
}

// 中序遍历
void traverse(TreeNode root) {
	traverse(root.left);
	inorder.add(root.val);
	traverse(root.right);
}

// 后序遍历
void traverse(TreeNode root) {
	traverse(root.left);
	traverse(root.right);
	postorder.add(root.val);
}
```

🔵 寻找二者的根节点、左右子树的下标关系

![img1](/img/labuladong/9-1.png)

🟠 通过【哈希映射】得出根节点在 inorder 中的下标。（对 vector 用 find 容易出错 😭）

```c++
class Solution { 
public:
    map<int,int> mp;
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        // 为inorder构造哈希映射
        for(int i=0;i<inorder.size();i++){
            mp.insert({inorder[i],i});
        }
        return build(preorder,0, preorder.size()-1, inorder,0,preorder.size()-1);
    }

    TreeNode* build(vector<int>& preorder, int pl, int pr, vector<int>& inorder, int il, int ir){
        // base case
        if(pl>pr) return nullptr;

        // 构造根节点
        int rootVal = preorder[pl];
        TreeNode* root = new TreeNode(rootVal);

        // 左右递归
        // 这里找坐标时直接用vector的find方法容易出错，改用哈希映射
        int idx = mp[rootVal];
        root->left = build(preorder, pl+1, pl+idx-il, inorder, il, idx-1);
        root->right = build(preorder, pl+idx-il+1, pr, inorder, idx+1, ir);
        return root;
    }
};
```



### 3  从中序与后序遍历序列构造二叉树

#### 题目

🔗力扣 [106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

给定两个整数数组 `inorder` 和 `postorder` ，其中 `inorder` 是二叉树的中序遍历，`postorder` 是同一棵树的后序遍历，请你构造并返回这颗 *二叉树* 。

#### 解析

🔵 思路和上一题类似，不过在寻找二者的根节点、左右子树的下标关系时有所不同：

![img1](/img/labuladong/9-2.png)

```c++
class Solution {
public:
    map<int,int> mp;
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        // 构造哈希映射
        for(int i=0;i<inorder.size();i++){
            mp.insert({inorder[i],i});
        }

        return build(inorder,0,inorder.size()-1,postorder,0,inorder.size()-1);
    }

    TreeNode* build(vector<int>& inorder, int il, int ir, vector<int>& postorder, int pl, int pr){
        if(il>ir)   return nullptr;

        // 构造根节点
        int rootVal = postorder[pr];
        TreeNode* root = new TreeNode(rootVal);

        // 递归左右子树
        int idx = mp[rootVal];
        // 注意递归时的左右下标
        root->left = build(inorder,il,idx-1,postorder,pl,pl+idx-il-1);
        root->right = build(inorder,idx+1,ir,postorder,pl+idx-il,pr-1);
        return root;
    }
};
```



### 4 根据前序和后序遍历构造二叉树

#### 题目

🔗力扣 [889. 根据前序和后序遍历构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)

给定两个整数数组，`preorder` 和 `postorder` ，其中 `preorder` 是一个具有 **无重复** 值的二叉树的前序遍历，`postorder` 是同一棵树的后序遍历，重构并返回二叉树。

如果存在多个答案，您可以返回其中 **任何** 一个。

#### 解析

🔵 通过前序中序，或者后序中序遍历结果可以确定唯一一棵原始二叉树， **但是通过前序后序遍历结果无法确定唯一的原始二叉树。**  

🟡 用后序遍历和前序遍历构造二叉树，思路上同样是通过控制左右子树的索引来构建：

📌 **把前序遍历的第⼀个元素（即后序遍历的最后⼀个元素）确定为根节点。**

📌 **把前序遍历的第二个元素作为左子树的根节点。**

📌 在后序遍历中寻找左子树根节点的下标，从而确定左子树的索引边界，进而确定右子树的索引边
界，递归构造左右子树。  

![img1](/img/labuladong/9-3.png)

```c++
class Solution {
public:
    map<int,int> mp;
public:
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        // 为postorder构造哈希映射
        for(int i=0;i<postorder.size();i++){
            mp.insert({postorder[i],i});
        }

        return build(preorder,0,preorder.size()-1,postorder,0,preorder.size()-1);
    }

    TreeNode* build(vector<int>& preorder, int pl, int pr, vector<int>& postorder, int ql, int qr){
        if(pl>pr)   return nullptr;
        // 这句很重要！！！
        if(pl==pr)  return (new TreeNode(preorder[pl]));

        // 构造根节点
        int rootVal = preorder[pl];
        int nextVal = preorder[pl+1];
        TreeNode* root = new TreeNode(rootVal);

        // 左右递归
        // 注意是找左子树节点的下标！！！
        int idx = mp[nextVal];
        root->left = build(preorder,pl+1,pl+1+idx-ql,postorder,ql,idx);
        root->right = build(preorder,pl+2+idx-ql,pr,postorder,idx+1,qr-1);
        return root;
    }
};
```




