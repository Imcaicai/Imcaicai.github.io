# 数组链表-双指针技巧解决数组题


在处理数组和链表相关问题时，双指针技巧是经常用到的，双指针技巧主要分为两类：左右指针和快慢指针。所谓左右指针，就是两个指针相向而行或者相背而行；而所谓快慢指针，就是两个指针同向而行。  

# 1 快慢指针技巧

### 题目

**力扣 [26. 删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)**

给你一个 **升序排列** 的数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序** 应该保持 **一致** 。

将最终结果插入 `nums` 的前 `k` 个位置后返回 `k` 。

不要使用额外的空间，你必须在 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组** 并在使用 O(1) 额外空间的条件下完成。

### 解析

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

### 进阶1

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

### 进阶2

**力扣 [27. 移除元素](https://leetcode.cn/problems/remove-element/)**

给你一个数组 `nums` 和一个值 `val`，你需要 **[原地](https://baike.baidu.com/item/原地算法)** 移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 `O(1)` 额外空间并 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组**。

##### 解析：

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

### 进阶3

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

# 2 左右指针技巧





```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left=0, right=numbers.length-1;
        while(left<right){
            if(numbers[left]+numbers[right]==target)
                return new int[]{left+1,right+1};
            else if(numbers[left]+numbers[right]<target)
                left++;
            else right--;
        }
        
        return new int[]{-1,-1};
    }

}
```



```java
class Solution {
    public void reverseString(char[] s) {
        int left=0, right=s.length-1;
        char temp;
        while(left<right){
            temp=s[left];
            s[left]=s[right];
            s[right]=temp;

            left++;
            right--;
        }
    }
}
```



```java
class Solution {
    public String longestPalindrome(String s) {
        String res1,res2,res="";
        for(int i=0; i<s.length(); i++){
            res1 = palindrome(s,i,i);
            res2 = palindrome(s,i,i+1);
            res = res.length()>res1.length() ? res : res1;
            res = res.length()>res2.length() ? res : res2;
        }

        return res;
    }

    public String palindrome(String s, int l, int r){
        while(l>=0 && r<s.length() && s.charAt(l)==s.charAt(r)){
            l--;
            r++;
        }

        return s.substring(l+1,r);
    }
}
```






