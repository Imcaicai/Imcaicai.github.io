# 【动态规划】动态规划核心框架


🔴 **【动态规划三要素】重叠子问题、最优子结构、状态转移方程**

🟢 **【思维框架】明确 base case → 明确「状态」→ 明确「选择」 → 定义 dp 数组/函数的含义。**

🔵

```Python
# ⾃顶向下递归的动态规划
def dp(状态1, 状态2, ...):
     for 选择 in 所有可能的选择:
     # 此时的状态已经因为做了选择⽽改变
     result = 求最值(result, dp(状态1, 状态2, ...))
     return result

# ⾃底向上迭代的动态规划
# 初始化 base case
dp[0][0][...] = base case
# 进⾏状态转移
for 状态1 in 状态1的所有取值：
     for 状态2 in 状态2的所有取值：
         for ...
             dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```



## 1 【重叠子问题】

### 题目——斐波那契数

**力扣 [509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/)**

**斐波那契数** （通常用 `F(n)` 表示）形成的序列称为 **斐波那契数列** 。该数列由 `0` 和 `1` 开始，后面的每一项数字都是前面两项数字的和。

给定 `n` ，请计算 `F(n)` 。

### 1.1 暴力递归

```c++
int fib(int N) {
     if (N == 1 || N == 2) return 1;
     return fib(N - 1) + fib(N - 2);
}
```

子问题个数： O(2^n)。解决⼀个子问题的时间：只有 f(n - 1) + f(n - 2) ⼀个加法操作， 时间为 O(1)。因此这个算法的时间复杂度为二者相乘，即 O(2^n)。

这种方法存在大量重复计算，即重叠子问题，我们需要想办法解决这个问题。

### 1.2 带备忘录的递归解法(自顶向下)

```c++
int fib(int N) {
     // 备忘录全初始化为 0
     int[] memo = new int[N + 1];
     // 进⾏带备忘录的递归
     return helper(memo, N);
}

int helper(int[] memo, int n) {
     // base case
     if (n == 0 || n == 1)	return n;
         // 已经计算过，不⽤再计算了
     if (memo[n] != 0)	return memo[n];
     memo[n] = helper(memo, n - 1) + helper(memo, n - 2);
     return memo[n];
}
```

在这种算法中，由于不存在冗余计算，子问题个数为 O(n)，解决一个子问题的时间仍为 O(1)。 所以算法的时间复杂度是 O(n)，和暴力算法相比提升很多。

### 1.3 dp 数组的迭代/递推解法(自低向上)

```c++
int fib(int N) {
     if (N == 0) return 0;
     int[] dp = new int[N + 1];
     // base case
     dp[0] = 0; dp[1] = 1;
     // 状态转移
     for (int i = 2; i <= N; i++) {
         dp[i] = dp[i - 1] + dp[i - 2];
     }
     return dp[N];
}
```

根据斐波那契数列的状态转移方程（ f(n) = f(n-1) + f(n-2) ），当前状态只和之前的两个状态有关，其实并不需要 用数组来存储所有状态，只要存储当前状态的前两个就行。 所以，可以进一步优化，把空间复杂度降为 O(1)：

```c++
int fib(int n) {
     if (n == 0 || n == 1) {
     // base case
     return n;
	}
    
	// 分别代表 dp[i - 1] 和 dp[i - 2]
	int dp_i_1 = 1, dp_i_2 = 0;
	for (int i = 2; i <= n; i++) {
		// dp[i] = dp[i - 1] + dp[i - 2];
     	int dp_i = dp_i_1 + dp_i_2;
     	// 滚动更新
     	dp_i_2 = dp_i_1;
     	dp_i_1 = dp_i;
 	}
 	return dp_i_1;
}
```

### 2 【转移方程】

### 题目——零钱兑换

**力扣 [322. 零钱兑换](https://leetcode.cn/problems/coin-change/)**

给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。

计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。

你可以认为每种硬币的数量是无限的。

### 2.1 暴力递归

🔴 **确定 base case** ：目标金额 amount 为 0 时算法返回 0，因为不需要任何硬币就已经凑出目标金额了。 

🟡 **确定「状态」** ，也就是子问题中会变化的变量。在本题中是目标金额 amount。 

🟢 **确定「选择」** ，也就是导致「状态」产生变化的行为。在本题中为所有硬币的面值。 

🔵 **明确 dp 函数/数组的定义** 。即凑出目标金额所需的最少硬币数量。

```c++
int coinChange(int[] coins, int amount) {
     // 题⽬要求的最终结果是 dp(amount)
     return dp(coins, amount)
}

// 定义：要凑出⾦额 n，⾄少要 dp(coins, n) 个硬币
int dp(int[] coins, int amount) {
     // base case
     if (amount == 0) return 0;
     if (amount < 0) return -1;
     int res = Integer.MAX_VALUE;
     for (int coin : coins) {
    	// 计算⼦问题的结果
     	int subProblem = dp(coins, amount - coin);
     	// ⼦问题⽆解则跳过
     	if (subProblem == -1) continue;
     	// 在⼦问题中选择最优解，然后加⼀
     	res = Math.min(res, subProblem + 1);
     }
     return res == Integer.MAX_VALUE ? -1 : res;
}
```

假设目标金额为 n，给定的硬币个数为 k，那么子问题个数在 k^n 这个数量级。而解决每个子问题的复杂度为 O(k)，相乘得到总时间复杂度为 O(k^n)。

### 2.2 带备忘录的递归

```c++
int[] memo;
int coinChange(int[] coins, int amount) {
     memo = new int[amount + 1];
     // 备忘录初始化为⼀个不会被取到的特殊值，代表还未被计算
     Arrays.fill(memo, -666);
     return dp(coins, amount);
}

int dp(int[] coins, int amount) {
     if (amount == 0) return 0;
     if (amount < 0) return -1;
     // 查备忘录，防⽌重复计算
     if (memo[amount] != -666)
     return memo[amount];
     int res = Integer.MAX_VALUE;
     for (int coin : coins) {
         // 计算⼦问题的结果
         int subProblem = dp(coins, amount - coin);
         // ⼦问题⽆解则跳过
         if (subProblem == -1) continue;
         // 在⼦问题中选择最优解，然后加⼀
         res = Math.min(res, subProblem + 1);
     }
     // 把计算结果存⼊备忘录
     memo[amount] = (res == Integer.MAX_VALUE) ? -1 : res;
     return memo[amount];
}
```

子问题数目为 O(n)。处理⼀个子问题的时间不变，仍是 O(k)，总的时间复杂度是 O(kn)。

### 2.3 dp 数组的迭代解法

**dp 数组的定义：当目标金额为 i 时，至少需要 dp[i] 枚硬币凑出。**

```c++
int coinChange(int[] coins, int amount) {
     int[] dp = new int[amount + 1];
     // 数组⼤⼩为 amount + 1，初始值也为 amount + 1
     Arrays.fill(dp, amount + 1);
     // base case
     dp[0] = 0;
     // 外层 for 循环在遍历所有状态的所有取值
     for (int i = 0; i < dp.length; i++) {
     	// 内层 for 循环在求所有选择的最⼩值
     	for (int coin : coins) {
     		// ⼦问题⽆解，跳过
     		if (i - coin < 0) {
         		continue;
     		}
     		dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
 		}
 	}
 	return (dp[amount] == amount + 1) ? -1 : dp[amount];
}
```


