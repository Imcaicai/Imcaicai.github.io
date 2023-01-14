# 【数组链表】双指针技巧解决数组题


在处理数组和链表相关问题时，双指针技巧是经常用到的，双指针技巧主要分为两类：左右指针和快慢指针。所谓左右指针，就是两个指针相向而行或者相背而行；而所谓快慢指针，就是两个指针同向而行。  

## 1 快慢指针技巧

#### 题目

**力扣 [26. 删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)**

给你一个 **升序排列** 的数组 `nums` ，请你 **[ 原地](http://baike.baidu.com/item/原地算法)**  删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序**  应该保持 **一致** 。

将最终结果插入 `nums` 的前 `k` 个位置后返回 `k` 。

不要使用额外的空间，你必须在 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组** 并在使用 O(1) 额外空间的条件下完成。

#### 解析

我们让慢指针 slow 走在后面，快指针 fast 走在前面探路，找到⼀个不重复的元素就赋值给 slow 并让
slow 前进⼀步。
这样，就保证了 nums[0..slow] 都是无重复的元素，当 fast 指针遍历完整个数组 nums 后，nums[0..slow] 就是整个数组去重之后的结果。  代码实现：

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums.length == 0)    return 0;
        int slow=0,fast=0;
        while(fast<nums.length){
            if(nums[slow] != nums[fast]){
                slow++;
                nums[slow]=nums[fast];
            }
            fast++;
        }
        
        return slow+1;
    }
}
```

#### 进阶1

**力扣 [83. 删除排序链表中的重复元素](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)**

给定一个已排序的链表的头 `head` ， 删除所有重复的元素，使每个元素只出现一次 。返回已排序的链表 。

代码实现：

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null)    return head;
        ListNode slow=head,fast=head;
        while(fast != null){
            if(slow.val != fast.val){
                slow.next = fast;
                slow = slow.next;
            }
            fast = fast.next;
        }
        slow.next = null;
        return head;
    }
}
```

#### 进阶2

**力扣 [27. 移除元素](https://leetcode.cn/problems/remove-element/)**

给你一个数组 `nums` 和一个值 `val`，你需要 **[原地](https://baike.baidu.com/item/原地算法)** 移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 `O(1)` 额外空间并 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组**。

**解析：**

和前面类似，依然需要使用快慢指针技巧。如果 fast 遇到值为 val 的元素，则直接跳过，否则就赋值给 slow 指针，并让 slow 前进⼀步。  代码实现：

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int fast = 0, slow = 0;
        while (fast < nums.length) {
            if (nums[fast] != val) {
                nums[slow] = nums[fast];
                slow++;
            }
            fast++;
        }
        return slow;
    }
}
```

#### 进阶3

**力扣 [283. 移动零](https://leetcode.cn/problems/move-zeroes/)**

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

和上一题思路类似，代码实现：

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int fast = 0, slow = 0;
        while (fast < nums.length) {
            if (nums[fast] != 0) {
                nums[slow] = nums[fast];
                slow++;
            }
            fast++;
        }

        for(int i=slow; i<nums.length; i++)
            nums[i] = 0;
    }
}
```

## 2 左右指针技巧

### 2.1 二分查找

```java
int binarySearch(int[] nums, int target) {
	// ⼀左⼀右两个指针相向⽽⾏
	int left = 0, right = nums.length - 1;
	while(left <= right) {
		int mid = (right + left) / 2;
		if(nums[mid] == target)
			return mid;
		else if (nums[mid] < target)
			left = mid + 1;
		else if (nums[mid] > target)
			right = mid - 1;
	}
	return -1;
}
```

### 2.2 两数之和

#### 题目

**力扣 [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)**

给你一个下标从 **1** 开始的整数数组 `numbers` ，该数组已按 **非递减顺序排列** ，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。如果设这两个数分别是 `numbers[index1]` 和 `numbers[index2]` ，则 `1 <= index1 < index2 <= numbers.length` 。以长度为 2 的整数数组 `[index1, index2]` 的形式返回这两个整数的下标 `index1` 和 `index2`。

你可以假设每个输入 **只对应唯一的答案** ，而且你 **不可以** 重复使用相同的元素。你所设计的解决方案必须只使用常量级的额外空间。

#### 解析

使用双指针，通过调节 left 和 right 就可以调整 sum 的大小。代码实现：

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left=0, right=numbers.length-1;
        while(left<right){
            if(numbers[left]+numbers[right]==target)
                // 题目要求的索引是从1开始的
                return new int[]{left+1,right+1};
            else if(numbers[left]+numbers[right]<target)
                left++;	// 让和大一点
            else right--;	// 让和小一点
        }        
        return new int[]{-1,-1};
    }

}
```

### 2.3 反转数组

#### 题目

**力扣 [344. 反转字符串](https://leetcode.cn/problems/reverse-string/)**

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `s` 的形式给出。

不要给另外的数组分配额外的空间，你必须 **[原地 ](https://baike.baidu.com/item/原地算法) 修改输入数组**、使用 O(1) 的额外空间解决这一问题。

#### 解析

通过左右指针反转，思路很简单。

> 注意：temp 的声明在 while 循环里面内存占用会比下面的多很多。在以后的编程中要注意。

```java
class Solution {
    public void reverseString(char[] s) {
        int left=0, right=s.length-1;
        char temp;
        while(left<right){
            // 交换s[left]和s[right]
            temp=s[left];
            s[left]=s[right];
            s[right]=temp;

            left++;
            right--;
        }
    }
}
```

### 2.4 回文串判断

#### 解析

使用左右指针判断回文串，代码如下：

```java
boolean isPalindrome(String s) {
	// ⼀左⼀右两个指针相向⽽⾏
	int left = 0, right = s.length() - 1;
	while (left < right) {
		if (s.charAt(left) != s.charAt(right)) {
			return false;
		}
		left++;
		right--;
	}
	return true;
}
```

#### 进阶

**力扣 [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)**

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

**解析**

使用**从中心向两端扩散的双指针技巧**。如果回文串的长度为奇数，则它有⼀个中心字符；如果回文串的长度为偶数，则可以认为它有两个中心字符。  我们遍历整个数组，并分别求出以 s[i] 和 s[i,j] 为中心的最长回文串，保留两者中更长的，最终可以得到所求结果。

```java
class Solution {
    public String longestPalindrome(String s) {
        String res1,res2,res="";
        for(int i=0; i<s.length(); i++){
            // 以s[i]为中心的最长回文子串
            res1 = palindrome(s,i,i);
            // 以s[i]和s[i+1]为中心的最长回文子串
            res2 = palindrome(s,i,i+1);
            res = res.length()>res1.length() ? res : res1;
            res = res.length()>res2.length() ? res : res2;
        }
        return res;
    }

    // 在s中寻找以s[l]和s[r]为中心的最长回文串
    public String palindrome(String s, int l, int r){
        while(l>=0 && r<s.length() && s.charAt(l)==s.charAt(r)){
            l--;
            r++;
        }
        return s.substring(l+1,r);
    }
}
```

参考资料：

https://labuladong.github.io/algo/








