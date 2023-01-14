# 【数组链表】前缀和


**前缀和主要适⽤的场景是原始数组不会被修改的情况下，频繁查询某个区间的累加和。**  

## 1 一维数组中的前缀和

### 题目

**力扣 [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)**

给定一个整数数组  `nums`，计算索引 `left` 和 `right` （包含 `left` 和 `right`）之间的 `nums` 元素的 和 ，其中 `left <= right`，实现 `NumArray` 类：

- `NumArray(int[] nums)` 使用数组 `nums` 初始化对象
- `int sumRange(int i, int j)` 返回数组 `nums` 中索引 `left` 和 `right` 之间的元素的 总和 ，包含 `left` 和 `right` 两点（也就是 `nums[left] + nums[left + 1] + ... + nums[right] )`

示例：

```java
输入：
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
输出：
[null, 1, -1, -3]

解释：
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1)) 
numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))
```

### 解析

不使用前缀和的做法：

```java
class NumArray {
    private int[] nums;

    public NumArray(int[] nums) {
        this.nums=nums;
    }
    
    public int sumRange(int left, int right) {
        int sum=0;
        for(int i=left;i<=right;i++){
            sum+=nums[i];
        }
        return sum;
    }
}
```

可以达到效果，但是效率很差，因为 sumRange ⽅法会被频繁调⽤，⽽它的时间复杂度是 **O(N)**，其中 N 代表 nums 数组的⻓度。使⽤前缀和后， sumRange 函数的时间复杂度降为 **O(1)**，避免使用 for 循环。

前缀和原理：

![img1](/img/labuladong/1-1.png)

代码实现：

```java
class NumArray {
    // 前缀和数组
    private int[] sum;

    public NumArray(int[] nums) {
        sum=new int[nums.length+1];
        sum[0]=0;
        // 计算 nums 的累加和
        for(int i=1;i<=nums.length;i++)
            sum[i]=sum[i-1]+nums[i-1];
    }
    
    public int sumRange(int left, int right) {
        return sum[right+1]-sum[left];
    }
}
```

### 延伸

这个技巧在⽣活中运⽤也挺⼴泛的，⽐⽅说，你们班上有若⼲同学，每个同学有⼀个期末考试的成绩（满分
100 分），那么请你实现⼀个 API，输⼊任意⼀个分数段，返回有多少同学的成绩在这个分数段内。
那么，你可以先通过计数排序的⽅式计算每个分数具体有多少个同学，然后利⽤前缀和技巧来实现分数段查
询的 API：  

```java
int[] scores; // 存储着所有同学的分数
// 试卷满分 100 分
int[] count = new int[100 + 1]
// 记录每个分数有⼏个同学
for (int score : scores)
count[score]++
// 构造前缀和
for (int i = 1; i < count.length; i++)
count[i] = count[i] + count[i-1];
// 利⽤ count 这个前缀和数组进⾏分数段查询
```

## 2 二维数组中的前缀和

### 题目

**力扣 [304. 二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)**

给定一个二维矩阵 `matrix`，计算其子矩形范围内元素的总和，该子矩阵的 左上角 为 `(row1, col1)` ，右下角 为 `(row2, col2)` 。实现 `NumMatrix` 类：

- `NumMatrix(int[][] matrix)` 给定整数矩阵 `matrix` 进行初始化
- `int sumRegion(int row1, int col1, int row2, int col2)` 返回 左上角 `(row1, col1)` 、右下角 `(row2, col2)` 所描述的子矩阵的元素 总和 。

### 解析

和⼀维数组中的前缀和类似，我们可以维护⼀个⼆维 sum 数组，专⻔记录以原点为顶点的矩阵的元素之和，就可以⽤⼏次加减运算算出任何⼀个⼦矩阵的元素和，典型的 **“空间换时间”**。思路如图所示：

![img2](/img/labuladong/1-2.png)

代码实现：

```java
class NumMatrix {
    // sum[i][j] 记录 matrix 中⼦矩阵 [0, 0, i-1, j-1] 的元素和
    private int[][] sum;

    public NumMatrix(int[][] matrix) {
        int row=matrix.length;
        int column=matrix[0].length;
        sum=new int[row+1][column+1];	// 构造前缀和矩阵
        for(int i=1;i<=row;i++){
            for(int j=1;j<=column;j++){
                // 计算每个矩阵 [0, 0, i, j] 的元素和
                sum[i][j]=sum[i][j-1]+sum[i-1][j]-sum[i-1][j-1]+matrix[i-1][j-1];
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return sum[row2+1][col2+1]-sum[row2+1][col1]-sum[row1][col2+1]+sum[row1][col1];
    }
}
```



参考资料：

[https://labuladong.github.io/algo/](https://labuladong.github.io/algo/)

