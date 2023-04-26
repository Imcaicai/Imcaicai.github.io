# 【二叉树】归并排序详解及应用


### 1 算法思路

🔴 所有递归的算法，本质上都是在遍历一棵（递归）树，然后在节点（前中后序位置）上执行代码。

🔵 【归并排序】类似于【二叉树的后序遍历】，使用【分治算法】的思想。

🟡 递归的 sort 函数就是二叉树的遍历函数，而 merge 函数就是在每个节点上做
的事情  

```c++
// 定义：排序 nums[lo..hi]
void sort(int[] nums, int lo, int hi) {
	if (lo == hi) {
		return;
	}
	int mid = (lo + hi) / 2;
	// 利⽤定义，排序 nums[lo..mid]
	sort(nums, lo, mid);
	// 利⽤定义，排序 nums[mid+1..hi]
	sort(nums, mid + 1, hi);
	/****** 后序位置 ******/
	// 此时两部分⼦数组已经被排好序
	// 合并两个有序数组，使 nums[lo..hi] 有序
	merge(nums, lo, mid, hi);
	/*********************/
}

// 将有序数组 nums[lo..mid] 和有序数组 nums[mid+1..hi]
// 合并为有序数组 nums[lo..hi]
void merge(int[] nums, int lo, int mid, int hi);
```

### 2 代码实现

🟠 sort 函数对 nums[lo..mid] 和 nums[mid+1..hi] 递归排序完成之后，我们没有办法原地把它俩合并，所以需要 copy 到 temp 数组里面。

🟡 通过类似于【合并有序链表的双指针技巧】将nums[lo..hi] 合并成⼀个有序数组。

🟢 时间复杂度  O(NlogN) 。（二叉树高度：logN，每层元素的个数：N）

```c++
class Merge {
	// ⽤于辅助合并有序数组
	private static int[] temp;
	public static void sort(int[] nums) {
		// 先给辅助数组开辟内存空间
		temp = new int[nums.length];
		// 排序整个数组（原地修改）
		sort(nums, 0, nums.length - 1);
	}
    
	// 定义：将⼦数组 nums[lo..hi] 进⾏排序
	private static void sort(int[] nums, int lo, int hi) {
		if (lo == hi) {
			// 单个元素不⽤排序
			return;
		}
		// 这样写是为了防⽌溢出，效果等同于 (hi + lo) / 2
		int mid = lo + (hi - lo) / 2;
        // 先对左半部分数组 nums[lo..mid] 排序
		sort(nums, lo, mid);
		// 再对右半部分数组 nums[mid+1..hi] 排序
		sort(nums, mid + 1, hi);
		// 将两部分有序数组合并成⼀个有序数组
		merge(nums, lo, mid, hi);
	}
   
	// 将 nums[lo..mid] 和 nums[mid+1..hi] 这两个有序数组合并成⼀个有序数组
	private static void merge(int[] nums, int lo, int mid, int hi) {
		// 先把 nums[lo..hi] 复制到辅助数组中
		// 以便合并后的结果能够直接存⼊ nums
		for (int i = lo; i <= hi; i++) {
			temp[i] = nums[i];
		}
		// 数组双指针技巧，合并两个有序数组
		int i = lo, j = mid + 1;
		for (int p = lo; p <= hi; p++) {
			if (i == mid + 1) {
				// 左半边数组已全部被合并
				nums[p] = temp[j++];
			} else if (j == hi + 1) {
				// 右半边数组已全部被合并
				nums[p] = temp[i++];
			} else if (temp[i] > temp[j]) {
				nums[p] = temp[j++];
			} else {
				nums[p] = temp[i++];
			}
		}
	}
}
```

### 3 例题

#### 3.1 计算右侧小于当前元素的个数

**【题目】**

🔗力扣 [315. 计算右侧小于当前元素的个数](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/)

给你一个整数数组 `nums` ，按要求返回一个新数组 `counts` 。数组 `counts` 有该性质： `counts[i]` 的值是 `nums[i]` 右侧小于 `nums[i]` 的元素的数量。

**【解析】**

![img1](/img/labuladong/10-1.png)

🟠 合并 nums[lo..hi] 的过程中，每当执行 nums[p] = temp[i] 时，就可以确定 temp[i] 后面比它小的元素个数为 j - mid - 1 。

🔵 只需在 merge 中添加两行代码即可解决。

```c++
class Solution {
private:
    vector<pair<int,int>> temp; // 记录临时的数据
    vector<pair<int,int>> node; // 记录原始数据，不知道为什么当做函数参数会报错，所以改成全局参数
    vector<int> ans;  // 记录答案

public:
    vector<int> countSmaller(vector<int>& nums) {
        // 将原数组转为Node结构
        temp.resize(nums.size());
        ans.resize(nums.size());
        node.resize(nums.size());   

        for(int i=0;i<nums.size();i++)
            node[i]={nums[i],i};

        // 归并排序，求出答案
        sort(0,nums.size()-1);
        return ans;
    }

    void sort(int l, int r){
        if(l>=r)    return;
        int mid = l+(r-l)/2;
        sort(l, mid);
        sort(mid+1, r);
        merge(l, mid, r);
    }

    void merge(int l, int mid, int r){
        for (int i = l; i <= r; i++) {
            temp[i] = node[i];
        }

        int i = l,j=mid+1;
        for(int k=l;k<=r;k++){
            if(i==mid+1)    node[k]=temp[j++];
            else if(j==r+1){
                node[k]=temp[i++];
                // 新增代码
                ans[node[k].second] += (j-mid-1);
            } 
            else if(temp[i].first>temp[j].first)    node[k]=temp[j++];
            else{
                node[k]=temp[i++];
                // 新增代码
                ans[node[k].second] += (j-mid-1);
            }
        }
    }
};
```

#### 3.2 翻转对

**【题目】**

🔗力扣 [493. 翻转对](https://leetcode.cn/problems/reverse-pairs/)

给定一个数组 `nums` ，如果 `i < j` 且 `nums[i] > 2*nums[j]` 我们就将 `(i, j)` 称作一个**重要翻转对**。

你需要返回给定数组中的重要翻转对的数量。

**【解析】**

🟠 当 nums[lo..mid] 和 nums[mid+1..hi] 两个子数组完成排序后，对于 nums[lo..mid] 中的每个元素 nums[i]，去 nums[mid+1..hi] 中寻找符合条件的 nums[j] 即可。

🟡 我们在找到的符合 nums[i] > 2×nums[j] 的 nums[j], mid+1 <= j <= hi，也必然也符合 nums[i+1] > 2×nums[j] 。因此不必每次都遍历 nums[mid+1...hi] 。

🟢 同样，只需要修改 merge 。

```c++
class Solution {
private:
    vector<int> temp;
    int ans = 0;    // 记录答案

public:
    int reversePairs(vector<int>& nums) {
        temp = nums;
        sort(nums,0,nums.size()-1);
        return ans;
    }

    void sort(vector<int>& nums, int l, int r){
        if(l==r)    return;
        int mid = (l+r)/2;
        sort(nums, l, mid);
        sort(nums, mid+1, r);
        merge(nums, l, mid, r);
    }

    void merge(vector<int>& nums, int l, int mid, int r){
        for (int i = l; i <= r; i++) {
            temp[i] = nums[i];
        }
		
        // 新增代码
        // 对于左半边的每个 nums[i]，都去右半边寻找符合条件的元素
        int end = mid+1;
        for(int i=l;i<=mid;i++){
            // nums 中的元素可能较⼤，乘 2 可能溢出，所以转化成 long
            while(end<=r && (long)nums[i]>(long)2*nums[end])
                end++;
            ans += (end-mid-1);
        }

        int i = l,j=mid+1;
        for(int k=l;k<=r;k++){
            if(i==mid+1)    nums[k]=temp[j++];
            else if(j==r+1) nums[k]=temp[i++];
            else if(temp[i]>temp[j])    nums[k]=temp[j++];
            else nums[k]=temp[i++];
        }
    }
};
```

#### 3.3 区间和的个数

**【题目】**

🔗力扣 [327. 区间和的个数](https://leetcode.cn/problems/count-of-range-sum/)

给你一个整数数组 `nums` 以及两个整数 `lower` 和 `upper` 。求数组中，值位于范围`[lower, upper]` （包含 `lower` 和 `upper`）之内的 **区间和的个数** 。

**区间和** `S(i, j)` 表示在 `nums` 中，位置从 `i` 到 `j` 的元素之和，包含 `i` 和 `j` (`i` ≤ `j`)。

**【解析】**

🟡 【前缀和】创建一个前缀和数组 preSum 来快速计算区间和。

🟢 在 merge 中添加代码，维护左闭右开区间 [start, end) 中的元素和 nums[i] 的差在 [lower, upper] 中。  

```c++
class Solution {
private:
    vector<long> temp;
    vector<long> preSum;    // 注意前缀和可能溢出，要用long存储
    int lower,upper,ans=0;
public:
    int countRangeSum(vector<int>& nums, int lower, int upper) {
        this->lower = lower;
        this->upper = upper;
        temp.resize(nums.size()+1);
        preSum.resize(nums.size()+1);

        // 构造前缀和
        for(int i=0;i<nums.size();i++)
            preSum[i+1] = (long)nums[i]+preSum[i];

        sort(preSum,0,preSum.size()-1);
        return ans;
    }

    void sort(vector<long>& nums, int l, int r){
        if(l==r)    return;
        int mid = (l+r)/2;
        sort(nums, l, mid);
        sort(nums, mid+1, r);
        merge(nums, l, mid, r);
    }

    void merge(vector<long>& nums, int l, int mid, int r){
        for (int i = l; i <= r; i++) {
            temp[i] = nums[i];
        }
		
        // 新增代码
        int begin=mid+1,end=mid+1;
        for(int i=l;i<=mid;i++){
            while(begin<=r && nums[begin]-nums[i]<lower)
                begin++;
            while(end<=r && nums[end]-nums[i]<=upper)
                end++;
            ans += (end-begin);
        }

        int i = l,j=mid+1;
        for(int k=l;k<=r;k++){
            if(i==mid+1)    nums[k]=temp[j++];
            else if(j==r+1) nums[k]=temp[i++];
            else if(temp[i]>temp[j])    nums[k]=temp[j++];
            else nums[k]=temp[i++];
        }
    }
};
```




