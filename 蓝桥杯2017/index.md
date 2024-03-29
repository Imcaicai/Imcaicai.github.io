# 【蓝桥杯】2017题解


⏰总用时：170/240			🎯总分：32.5/150

| 题号   | 时间 | 结果 | 满分 | 难度 | 备注                                                         |
| ------ | ---- | ---- | ---- | ---- | ------------------------------------------------------------ |
| **1**  | 45   | ✅    | 5    | 🌓    | 🔸 dfs<br/>🔸 注意考虑回路的情况                               |
| **2**  | 15   | ❌    | 11   | 🌓    | 🔹 考虑空盘子的跳法<br/>🔹 数据结构、bfs<br/>🔹 注意 set 容器的用法 |
| **3**  | 5    | ❌    | 13   | 🌑    | 🔸 Burnside引理                                               |
| **4**  | 5    | ❌    | 17   | 🌓    | 🔹 考虑分割线的走法<br/>🔹 【dfs】从对称中心开始遍历           |
| **5**  | 5    | ✅    | 7    | 🌕    |                                                              |
| **6**  | 10   | ✅    | 9    | 🌕    |                                                              |
| **7**  | 25   | ❌    | 19   | 🌓    | 🔸 dfs                                                        |
| **8**  | 5    | ❌    | 21   | 🌓    | 🔹 动态规划：要判断的包子数设置一个上限<br/>🔹 所有包子数最大公因数不为1，则凑不出来的有无数个 |
| **9**  | 30   | 50%  | 23   | 🌓    | 🔸 二分                                                       |
| **10** | 25   | ❌    | 25   |      |                                                              |

## 1 【填空】迷宫

### 题目

X 星球的一处迷宫游乐场建在某个小山坡上。它是由 10×10 相互连通的小房间组成的。房间的地板上写着一个很大的字母。我们假设玩家是面朝上坡的方向站立，则：

- L 表示走到左边的房间，
- R 表示走到右边的房间，
- U 表示走到上坡方向的房间，
- D 表示走到下坡方向的房间。

X 星球的居民有点懒，不愿意费力思考。他们更喜欢玩运气类的游戏。这个游戏也是如此！开始的时候，直升机把 100 名玩家放入一个个小房间内。玩家一定要按照地上的字母移动。迷宫地图如下：

```
UDDLUULRUL
UURLLLRRRU
RRUURLDLRD
RUDDDDUUUU
URUDLLRRUU
DURLRLDLRL
ULLURLLRDU
RDLULLRDDD
UUDDUDUDLL
ULRDLUURRR
```

请你计算一下，最后，有多少玩家会走出迷宫，而不是在里边兜圈子？

### 解析

🟠 **【dfs】** 遍历地图的坐标，用备忘录记录每个坐标点是否能走出地图，减少递归次数

🔵 注意如果某个点 (x, y) 已经被访问过并且没有被标记为能出去，则直接返回 false 。 **即如果有回路绕回来，也不能走出迷宫。**

```c++
#include <bits/stdc++.h>
using namespace std;

char c[10][10];							// 记录整个迷宫
bool d[10][10];							// 存储该坐标是否能出去
bool v[10][10];							// 存储该坐标是否已访问过		

bool dfs(int x,int y){
	if(v[x][y]){						
		if(d[x][y])	return true;		// 已知当前坐标是否可以走出迷宫，直接返回 
		else return false;				// 如果有回路绕回来，也是直接返回 false 
	}						
	
	v[x][y]=true;						// 先标记该点已访问过 
	switch(c[x][y]){					// 未知，则继续朝着坐标的方向走 
		case 'U':			
			if(x<=0)					// 可以出去，则返回并记录 
				return d[x][y]=true;					
			else						// 否则继续递归 
				return d[x][y]=dfs(x-1,y);		
		case 'D':
			if(x>=9)					 
				return d[x][y]=true;	
			else					
				return d[x][y]=dfs(x+1,y);
		case 'L':
			if(y<=0)					 
				return d[x][y]=true;	
			else			
				return d[x][y]=dfs(x,y-1);
		case 'R':
			if(y>=9)					 
				return d[x][y]=true;	
			else					
				return d[x][y]=dfs(x,y+1);
	}
}

int main(){

	for(int i=0;i<10;i++){			// 输入 
		for(int j=0;j<10;j++)
			cin>>c[i][j];
	}
	
	for(int i=0;i<10;i++){			// 深搜，确定每个坐标是否能走出迷宫 
		for(int j=0;j<10;j++)
			dfs(i,j);
	}
	
	int cnt=0;
	for(int i=0;i<10;i++){			// 统计能走出迷宫的个数 
		for(int j=0;j<10;j++)
			cnt+=d[i][j];
	}
	cout<<cnt;
	
	return 0;
} 
```

## 2 【填空】跳蚱蜢

### 题目

如下图所示： 有 9 只盘子，排成 1 个圆圈。 其中 8 只盘子内装着 8 只蚱蜢，有一个是空盘。 我们把这些蚱蜢顺时针编号为 1 ~ 8。

![img1](/img/蓝桥杯/8.png)

每只蚱蜢都可以跳到相邻的空盘中， 也可以再用点力，越过一个相邻的蚱蜢跳到空盘中。

请你计算一下，如果要使得蚱蜢们的队形改为按照逆时针排列， 并且保持空盘的位置不变（也就是 1−8 换位，2−7换位,...），至少要经过多少次跳跃？

### 解析

🟠 **不考虑蚱蜢的跳法，而考虑空盘子的跳法。** 这两个是等价的，但是空盘子的跳法只有固定 4 种：向左2格，向左1格，向右2格，向右1格。

🔵 **【数据结构】** 用 **node** 记录每个状态，其中： string 记录9个盘子的摆放状态，idx 记录空盘子的索引，cnt 记录达到这个状态的交换次数。用 **容器 set** 存储所有遍历过的盘子状态。用 **队列queue** 进行广度优先遍历。

🟡 **【bfs】** 每次遍历空盘子的 4 种走向。若当前状态未遍历过，则加入对应容器。

🟢 注意 set 容器的 find 用法❗❗❗

```c++
#include <bits/stdc++.h>
using namespace std;

struct node{
	string s;		// 盘子的摆放状态
	int idx;		// 空盘子的索引
	int cnt=0;		// 到达这个状态需要的步数 
};
set<string> s;		// 记录所有的状态 s 
queue<node> q;		// 每次 bfs 的队列 

void bfs(node x, int y){					// 当前的状态、下一步往哪走 
	string str=x.s;
	swap(str[x.idx],str[(x.idx+y+9)%9]);	// 更新跳跃后的字符串，注意取模模拟圆环
	if(s.find(str)!=s.end())	return;		// 此情况已经经历过，注意 set 的 find 用法！！！ 
	
	node n;n.s=str;n.idx=(x.idx+y+9)%9;n.cnt=x.cnt+1;
	q.push(n);s.insert(str);				// 此情况未经历过，加入此情况 
}

int main(){
	node n;n.s="012345678";n.idx=0;n.cnt=0;
	q.push(n);
	while(!q.empty()){
		node t=q.front();
		if(t.s=="087654321"){
			cout<<t.cnt;return 0;
		}
		bfs(t,-2);bfs(t,-1);				// 空盘子左右跳 2 或 1格 
		bfs(t,1);bfs(t,2);
		q.pop();
	}	
	
	return 0;
} 
```

## 3 【填空】魔方状态

### 题目

二阶魔方就是只有 2 层的魔方，只由 8 个小块组成。 如图所示。

![img1](/img/蓝桥杯/9.png)

小明很淘气，他只喜欢 3 种颜色，所有把家里的二阶魔方重新涂了颜色，如下。前面：橙色，右面：绿色，上面：黄色，左面：绿色，下面：橙色，后面：黄色。

请你计算一下，这样的魔方被打乱后，一共有多少种不同的状态。

如果两个状态经过魔方的整体旋转后，各个面的颜色都一致，则认为是同一状态。

### 解析

🟠 **【Burnside引理】** 不会。

```c++
229878
```

## 4 【填空】方格分割

### 题目

6x6的方格，沿着格子的边线剪开成两部分。 要求这两部分的形状完全相同。如下就是三种可行的分割法。

![img1](/img/蓝桥杯/10.png)

试计算： 包括这 3 种分法在内，一共有多少种不同的分割方法。 注意：旋转对称的属于同一种分割法。

### 解析

🟠 **考虑分割线的走法而不是方格的走法。** 左上角的点坐标为 (0, 0)，右上角的点坐标为 (6, 6)。我们从对称中心 (3, 3) 开始画一条分割线，当分割线走到边界，即 `（x == 0 || y == 0 || x == 6 || y == 6）` 时，分割线划分完毕，这就是一种分割方法。

🟡 **【dfs】** 对每个坐标遍历上下左右四种情况，注意分割线也是中心对称的，我们在给每个走过的分割点标记时，也要给其对称的点标记。

🔵 考虑到每种情况可以按 4 条边旋转，最终结果要除 4。

```c++
#include <bits/stdc++.h>
using namespace std;

int cnt=0;								// 总情况数 
int v[7][7];							// 标记每个网格点是否被访问 
int a[4]={-1,1,0,0};					// 坐标变化情况：上、下、左、右 
int b[4]={0,0,-1,1};

void dfs(int x,int y){
	if(x==0||x==6||y==0||y==6){			// 如果分割线划到了边界，这种情况遍历结束 
		cnt++;return;
	}
	
	for(int i=0;i<4;i++){
		if(v[x+a[i]][y+b[i]]==0){
			v[x+a[i]][y+b[i]]=1;
			v[6-x-a[i]][6-y-b[i]]=1;	// 中心对称的点也要被标记 
			dfs(x+a[i],y+b[i]);
			v[x+a[i]][y+b[i]]=0;
			v[6-x-a[i]][6-y-b[i]]=0;
		}
	}
}

int main(){
	
	memset(v,0,sizeof(v));
	v[3][3]=1;
	dfs(3,3);							// 从最中心开始遍历 
	cout<<cnt/4;						// 去重旋转的 
	
	return 0;
} 
```

## 5 【填空】字母组串

### 题目

由 A,B,C 这3个字母就可以组成许多串。 比如："A","AB","ABC","ABA","AACBB" ....

现在，小明正在思考一个问题： 如果每个字母的个数有限定，能组成多少个已知长度的串呢？

他请好朋友来帮忙，很快得到了代码， 解决方案超级简单，然而最重要的部分却语焉不详。

请仔细分析源码，填写划线部分缺少的内容。

### 解析

```c++
#include <stdio.h>

// a个A，b个B，c个C 字母，能组成多少个不同的长度为n的串。
int f(int a, int b, int c, int n)
{
    if(a<0 || b<0 || c<0) return 0;
    if(n==0) return 1; 
    
    return f(a-1,b,c,n-1)+f(a,b-1,c,n-1)+f(a,b,c-1,n-1) ;  // 填空
}

int main()
{
    printf("%d\n", f(1,1,1,2));
    printf("%d\n", f(1,2,3,3));
    return 0;
}
```

## 6 【填空】最大公共子串

### 题目

最大公共子串长度问题就是： 求两个串的所有子串中能够匹配上的最大长度是多少。

比如："abcdkkk" 和 "baabcdadabc"， 可以找到的最长的公共子串是"abcd",所以最大公共子串长度为 4。

下面的程序是采用矩阵法进行求解的，这对串的规模不大的情况还是比较有效的解法。

请分析该解法的思路，并补全划线部分缺失的代码。

### 解析

🟠 注意 scanf，printf 格式化输入输出的使用

```c++
#include <stdio.h>
#include <string.h>

#define N 256
int f(const char* s1, const char* s2)
{
    int a[N][N];
    int len1 = strlen(s1);
    int len2 = strlen(s2);
    int i,j;
    
    memset(a,0,sizeof(int)*N*N);
    int max = 0;
    for(i=1; i<=len1; i++){
        for(j=1; j<=len2; j++){
            if(s1[i-1]==s2[j-1]) {
                a[i][j] = a[i-1][j-1]+1; 
                if(a[i][j] > max) max = a[i][j];
            }
        }
    }
    
    return max;
}

int main()
{
    printf("%d\n", f("abcdkkk", "baabcdadabc"));
    printf("%d\n", f("aaakkkabababa", "baabababcdadabc"));
    printf("%d\n", f("abccbaacbcca", "ccccbbbbbaaaa"));    
    printf("%d\n", f("abcd", "xyz"));
    printf("%d\n", f("ab", "ab"));
    return 0;
}
```

## 7 正则问题

### 题目

考虑一种简单的正则表达式：

只由 x ( ) | 组成的正则表达式。

小明想求出这个正则表达式能接受的最长字符串的长度。

例如 ((xx|xxx)x|(x|xx))xx 能接受的最长字符串是： xxxxxx，长度是 6。

**输入描述**

一个由 x()| 组成的正则表达式。输入长度不超过 100，保证合法。

**输出描述**

这个正则表达式能接受的最长字符串的长度。

### 解析

🟠 **【dfs】** 注意遇到 '('，'|'，'x' 时的不同情况❗❗❗

```c++
#include <bits/stdc++.h>
using namespace std;

string s; 
int len,idx=0;
int dfs(){							// idx：当前遍历的数组下标，函数返回最大的 x 数量 
	int ans=0;
	while(idx<len){
		char c=s[idx];
		if(c=='('){					// 最好不要在 switch 里面用字符串，容易出错！！！ 
			idx++;					// 遇到左括号跳过，继续遍历 
			ans+=dfs();
			idx++;					// 这里要加一！！！ 
		}
		else if(c=='|'){
			idx++;
			ans=max(ans,dfs());	// 遇到或，选最大的	
		}
		else if(c=='x'){
			ans++;idx++;		// 遇到 x，计数器加一就行
		}
		else break;
	}
	return ans;
}

int main(){
	cin>>s;
	len=s.length();
	int ans=dfs();
	cout<<ans;
	
	return 0;
} 
```

## 8 包子凑数

### 题目

小明几乎每天早晨都会在一家包子铺吃早餐。他发现这家包子铺有 N 种蒸笼，其中第 i 种蒸笼恰好能放 $A_i$ 个包子。每种蒸笼都有非常多笼，可以认为是无限笼。

每当有顾客想买 X 个包子，卖包子的大叔就会迅速选出若干笼包子来，使得这若干笼中恰好一共有 X 个包子。比如一共有 3 种蒸笼，分别能放 3、4 和 5 个包子。当顾客想买 11 个包子时，大叔就会选 2 笼 3 个的再加 1 笼 5 个的（也可能选出 1 笼 3 个的再加 2 笼 4 个的）。

当然有时包子大叔无论如何也凑不出顾客想买的数量。比如一共有 3 种蒸笼，分别能放 4、5 和 6 个包子。而顾客想买 7 个包子时，大叔就凑不出来了。

小明想知道一共有多少种数目是包子大叔凑不出来的。

**输入描述**

第一行包含一个整数 N ( 1≤*N*≤100 )。

以下 N 行每行包含一个整数  $A_i$  (1≤ $A_i$ ≤100 )。 

**输出描述**

一个整数代表答案。如果凑不出的数目有无限多个，输出 INF。

### 解析

🟠 **把要判断的包子数设置一个上限，只要前面能凑出来，后面都能凑出来**

🟡 **如果所有包子数的最大公因数不为 1，则凑不出来的有无数多个** ❗❗❗

🟢 **【动态规划】** 遍历每一个蒸笼和上限内的所有包子数，转移方程为： `dp[j]=dp[j]|dp[j-a[i]]`

```c++
 #include <bits/stdc++.h>
using namespace std;
#define max 100000				// 设定一个最大数，后面的一定都能凑出来 

int main(){
	int n,g;
	int a[105],dp[max];
	cin>>n;cin>>g;a[0]=g;
	for(int i=1;i<n;i++){
		scanf("%d",&a[i]);
		g=__gcd(g,a[i]);		// 所有包子数的最大公因数 
	}
	
	if(g!=1){
		cout<<"INF";return 0;	// 最大公因数不为 1，则凑不出来的有无穷多个 
	} 
	
	memset(dp,0,sizeof(dp));dp[0]=1;
	for(int i=0;i<n;i++){		// 遍历每一个蒸笼，动态规划 
		for(int j=a[i];j<max;j++){
			dp[j]=dp[j]|dp[j-a[i]];
		} 
	}
	
	int cnt=0;
	for(int i=1;i<max;i++){		// 计算凑不出的包子数 
		if(!dp[i])	cnt++;
	}
	
	cout<<cnt; 
	return 0;
} 
```

## 9 分巧克力

### 题目

儿童节那天有 K 位小朋友到小明家做客。小明拿出了珍藏的巧克力招待小朋友们。

小明一共有 N 块巧克力，其中第 i 块是 $H_i×W_i$ 的方格组成的长方形。为了公平起见，小明需要从这 N 块巧克力中切出 K 块巧克力分给小朋友们。切出的巧克力需要满足：

1. 形状是正方形，边长是整数;
2. 大小相同;

例如一块 6x5 的巧克力可以切出 6 块 2x2 的巧克力或者 2 块 3x3 的巧克力。

当然小朋友们都希望得到的巧克力尽可能大，你能帮小明计算出最大的边长是多少么？

**输入描述**

第一行包含两个整数 N, K ( $1≤N,K≤10^5 $)。

以下 N 行每行包含两个整数  $H_i×W_i$  ( $1≤H_i×W_i≤10^5$ )。

输入保证每位小朋友至少能获得一块 1x1 的巧克力。

**输出描述**

输出切出的正方形巧克力最大可能的边长。

### 解析

```c++
#include <bits/stdc++.h>
using namespace std;

int n,k;
int h[100005],w[100005];

// 检查分为边长为 t 的正方形是否符合条件 
bool check(int t){
    int sum=0;
    for(int i=0;i<n;i++){
        sum+=(h[i]/t)*(w[i]/t);
        if(sum>=k)                    // 可以分给的人数超过 k，返回true 
            return true;
    }
    return false; 
}

int find_max(){
    int l=1,r=100000,mid;
    while(l<=r){                    // 注意这个条件是 <= ！！！ 
        mid=(l+r)/2;
        if(check(mid))                // 正方形边长还可以继续变大 
            l=mid+1;
        else
            r=mid-1;
    }
    return r;
}

int main(){
    scanf("%d %d",&n,&k);
    for(int i=0;i<n;i++)
        scanf("%d %d",&h[i],&w[i]);
    
    cout<< find_max();
}
```

## 10 油漆面积

