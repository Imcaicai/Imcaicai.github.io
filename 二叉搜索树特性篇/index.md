# 【二叉搜索树】特性篇


### 1 二叉搜索树BST

> 🔴 对于 BST 的每一个节点 node，左子树节点的值都比 node 的值要小，右子树节点的值都比 node 的值大。
>
> 🔵 对于 BST 的每一个节点 node，它的左侧子树和右侧子树都是 BST。  

**二叉搜索树问题的核心是【利用中序位置】。** 以下代码可以将 BST 中每个节点的值升序打印出来：  

```c++
void traverse(TreeNode root) {
	if (root == null) return;
	traverse(root.left);
	// 中序遍历代码位置
	print(root.val);
	traverse(root.right);
}
```

后面两道题都是基于这个性质实现的。



### 2 二叉搜索树中第K小的元素

#### 题目

🔗力扣 [230. 二叉搜索树中第K小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)

给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 个最小元素（从 1 开始计数）。

#### 解析



```c++
class Solution {
private:
    int count=0,k,ans;
public:
    int kthSmallest(TreeNode* root, int k) {
        this->k = k;
        traverse(root);
        return ans;
    }

    void traverse(TreeNode* root){
        if(root==nullptr)   return;
        traverse(root->left);

        // 中序遍历位置，找到第k小的元素
        count++;
        if(count == k){
            ans = root->val;return;
        }

        traverse(root->right);
    }
};
```

#### 扩展

🔴 利用【BST 中序遍历就是升序排序结果】这个性质，寻找第 k 小的元素的时间复杂度是 O(N)。

🟡 将时间复杂度降到 O(logN) 

当前节点知道自己排名第 m 时，我可以比较 m 和 k 的大小，将时间复杂度降为 O(logN) 。

✔ 如果 m == k，显然就是找到了第 k 个元素，返回当前节点。

✔ 如果 k < m，那说明排名第 k 的元素在左子树，所以去左子树搜索第 k 个元素。

✔ 如果 k > m，那说明排名第 k 的元素在右子树，所以去右子树搜索第 k - m - 1 个元素。
🔵 每个节点需要记录，以自己为根的这棵二叉树有多少个节点。每个节点 node 就可以通过 node->left 推导出自己的排名。 



### 3 把二叉搜索树转换为累加树

#### 题目

🔗力扣 [538. 把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

给出二叉 **搜索** 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 `node` 的新值等于原树中大于或等于 `node.val` 的值之和。

#### 解析

🔴 还是利用【二叉搜索树的中序遍历】特性，只不过需要计算【后缀和】。

```c++
class Solution {
private:
    vector<int> v;
    int temp=0;
public:
    TreeNode* convertBST(TreeNode* root) {
        // 遍历二叉搜索树，得到升序的数组 v
        traverse(root);

        // 计算后缀和
        for(int i=v.size()-2;i>=0;i--)
            v[i] = v[i]+v[i+1];
        
        // 修改节点值
        traverseTree(root);
        return root;
    }

    void traverse(TreeNode* root){
        if(root==nullptr)   return;
        traverse(root->left);
        v.push_back(root->val);
        traverse(root->right);
    }

    void traverseTree(TreeNode* root){
        if(root==nullptr)   return;
        traverseTree(root->left);
        root->val = v[temp++];
        traverseTree(root->right);
    }
};
```


