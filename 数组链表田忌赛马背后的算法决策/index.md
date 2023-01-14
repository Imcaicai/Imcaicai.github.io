# 【数组链表】田忌赛马背后的算法决策


## 题目

**力扣 [870. 优势洗牌](https://leetcode.cn/problems/advantage-shuffle/)**

给定两个大小相等的数组 `nums1` 和 `nums2`，`nums1` 相对于 `nums2` 的*优势*可以用满足 `nums1[i] > nums2[i]` 的索引 `i` 的数目来描述。

返回 nums1 的**任意**排列，使其相对于 `nums2` 的优势最大化。

## 解析

这道题类似于【**田忌赛马**】，只不过马的数量变多了，精髓在于【**打得过就打，打不过就拿自己的垃圾和对方的精锐互换** 】。我们先分析【田忌赛马】，考虑以下 3 点：

🟡 如果田忌的 1 号选手 < 齐王的 1 号选手，显然应该用田忌垫底的马送人头，降低对方的战斗力。

🟢 如果田忌的 1 号选手 < 齐王的 1 号选手，则应该直接让两者相比。

🟠 **当出现第二种情况时，即 T1 > Q1 时，要不要节约战斗力，用 T2 对抗 Q1？**

答案是不需要。假设 T2 > Q1，那么不论换不换 T1，T1 和 T2 都能对抗所有的 Q，这种节约毫无意义。

根据以上思路，我们的策略是：

**将齐王和田忌的马按照战斗力排序，然后按照排名一一对比。如果田忌的马能赢，那就比赛，如果赢不了，那就换个垫底的来送人头，保存实力。**  

结合已学过的双指针技巧，代码实现如下：

```java
class Solution {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        int n=nums1.length;
		// 给 nums2 降序排序
        PriorityQueue<int[]> maxq = new PriorityQueue<>(
            (int[] pair1, int[] pair2) -> {
                return pair2[1] - pair1[1];
            }
        );
        for(int i=0; i<n; i++)
            maxq.offer(new int[] {i, nums2[i]});
        
        // 给 numsq 升序排序
        Arrays.sort(nums1);

        int left=0, right=n-1;
        int[] res = new int[n];
        while(!maxq.isEmpty()){
            int[] pair=maxq.poll();
            // val 是nums2 中的最大值，i 是对应索引
            int i=pair[0], val=pair[1];
            if(val<nums1[right]){
                // 最大值能比过就直接比
                res[i]=nums1[right--];
            }
            else{
                // 否则用最小值和最大值比
                res[i]=nums1[left++];
            }
        }

        return res;
    }
}
```

算法的时间复杂度，也就是二叉堆和排序的复杂度 O(nlogn)。  

参考资料：

https://labuladong.github.io/algo/


