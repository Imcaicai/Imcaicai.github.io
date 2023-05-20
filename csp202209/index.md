# 【CSP】202209题解


## 1 如此编码

🔗 **题目：[如此编码](http://118.190.20.162/view.page?gpid=T153)**

本来看到题目很迷茫来着，想着第一题怎么就那么难，后来发现题目最后有提示，看完之后醍醐灌顶， **【取模】** 确实是很妙的思路，可以积累下来。

![img1](/img/CSP/1.png)

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,m,a[30],c[30],b,t=0;
	c[0] = 1;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
		c[i] = c[i-1]*a[i];
	}
	for(int i=1;i<=n;i++){
		b = (m%c[i]-t)/c[i-1];
		t += c[i-1]*b;
		printf("%d ",b);
	}
	return 0;
} 
```

## 2 何以包邮

🔗 **题目：[何以包邮](http://118.190.20.162/view.page?gpid=T152)**

🔴 **【动态规划】** 设 **s** 为所有参考书价格总和，题目可以理解为在 **s-x** 价格内，最大化被删除的书价格总和，这样就可以把这个问题看作经典的 **01背包问题** 。

🔵 注意动态规划的基本做法：设数组；计算base case；写出动态转移方程。

🟡 `dp[i][j]` ：在a0~ai编号中选书，得到不超过 j 的最大书价。

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	// 输入 
	int n,x,s=0,t,a[40];
	scanf("%d %d",&n,&x);
	for(int i=0;i<n;++i){
		scanf("%d",&a[i]);
		s += a[i];
	}
	t = s-x;
	
	// 动态规划：计算base case 
	int dp[n][t+1]={0};
	for(int i=0;i<=t;i++)
		dp[0][i] = a[0]<i ? a[0] : 0;
		
	// 动态规划：状态转移 
	for(int i=1;i<n;++i){
		for(int j=0;j<=t;++j){
			if(j<a[i])	dp[i][j] = dp[i-1][j];
			else dp[i][j] = max(dp[i-1][j],dp[i-1][j-a[i]]+a[i]);
		}
	}
	
	printf("%d",s-dp[n-1][t]);
	return 0;
} 
```

## 3 防疫大数据

🔗 **题目：[防疫大数据](http://118.190.20.162/view.page?gpid=T151)**

🟠🟠🟠 这道题读题有点绕，最重要的是选择合适的数据结构，然后一步一步分析。重要结构如下：

✅ 首先考虑漫游数据 <d,u,r>，我们用结构 my 来存储，并构造一个 my 类型的容器存储风险用户： `vector<my> u1` 。这里之所以把 <d,u,r> 都存下来而不是只存 u，是为了方便后续判断用户某时某地的访问数据是否还有风险。

✅ 考虑风险地区。由于是否有风险是由日期、地区共同决定的， **我们使用 `map<pair<int,int>,bool> mp` 来表示某时某地是否有风险。**  `pair<int,int>` 类型变量为键，存储地区、日期； `bool` 型变量为值，存储是否有风险。

✅ 由于最终结果要按用户编号从小到大输出，且同一用户只能输出一次，所以我们 **选用有自动排序、自动去重功能的 set 容器** 。

🟡🟡🟡 解题步骤：

✅ 清除 mp 中日期超过 7 天的记录，提高效率。（ **注意删除时要用 it1，直接 erase(iter) 会报错** ，虽然我还不知道为什么QAQ）

✅ 添加风险地区： `mp[{p,j}]=1`

✅ 更新以前日期的漫游数据中的风险用户。因为日期更新了一天，所以先前存的风险用户可能已经不再有风险了，需要更新。注意题目要求是 **【对所有的 D∈[j.d, i]，地区 j.r 都在风险范围内】** ，中间有任意一天不属于风险用户也要丢掉， **所以不能只看 j.d 或 i 那天是否为风险用户** 。（如果中间某天非风险，则说明原来那条漫游数据无风险，只是后面某天用户又去了同一个地点，那个地点刚好仍有风险。这里有点绕，很容易出错QAQ）

✅ 更新今天漫游数据中的风险用户。和上一步的方法类似。

✅ 输出答案。取 u1 中的 u 加入答案 ans 中输出。之所以不直接输出 u1 中的 u ，是因为题目要求最终结果由小到大排列且无重复用户。

🔵🔵🔵 在步骤 3 中，我们新开了一个 `vector<my> u2` 来存放符合要求的数据，并让 u1=u2，而不是在 u1 中直接删改，是因为可以避免频繁修改数组，提高效率

🟣🟣🟣 这种要求复杂、代码变量较多的题目，一定要自习检查变量是否用错，好几个 bug 都是因为容器索引的参数写错。 💢💢💢

```c++
#include <bits/stdc++.h>
using namespace std;

struct my{
	int d;int u;int r;		// 一条漫游数据 
};
vector<my> u1;				// 记录当天有风险的用户的漫游数据，因为不能重复存储，所以用vector 
map<pair<int,int>,bool> mp;	// 记录有风险的地区。键：地区、日期，值：是否是风险地 

int main()
{
	int n,r,m,p,di,ui,ri;
	cin>>n;					// 输入天数 n
	for(int i=0;i<n;i++){
		cin>>r>>m;			// 输入风险地数量、漫游数据数量
		
		
		// 1. 清除 mp 中日期超过 7 天的记录
		for(map<pair<int,int>,bool>::iterator iter = mp.begin();iter!=mp.end();){
			// 注意这里要用 it1，直接 erase(iter) 会报错 
			map<pair<int,int>,bool>::iterator it1 = iter;		
			iter++;												
			// 每次新的一天，检查是否有 7 天外的风险地区数据，有就删掉
			if(i - ((it1->first).second) >= 7)	mp.erase(it1);							
		}
		
		
		// 2. 添加风险地区 
		for(int j=0;j<r;j++){
			cin>>p;
			for(int j=i;j<i+7;j++)
				mp[{p,j}]=1;			// 这里的参数原来写错了 
		}
		
		
		// 3. 更新以前日期的漫游数据中的风险用户
		vector<my> u2;
		for(auto j:u1){					// 这里原来写错了，是 u1 而不是 u2 
			if(i-j.d >= 7)				// 已经是 7 天前的数据了 
				continue;
			
			int flag=1;
			for(int k=j.d;k<=i;k++){
				if(!mp[{j.r, k}]){		// 不满足：对所有的 D∈[j.d, i]，地区 j.r 都在风险范围内 
					flag=0;break;
				}
			}
			
			if(flag)					// 只插入从漫游时间到现在，都满足风险的数据 
				u2.push_back(j); 
		}
		u1.clear();						// 更新 u1
		u1=u2;
		
		
		// 4. 更新今天漫游数据中的风险用户 
		for(int j=0;j<m;j++){
			cin>>di>>ui>>ri;
			if(i-di>=7)	continue;		// 7 天外的数据不用考虑
			int flag=1;
			for(int k=di;k<=i;k++){
				if(!mp[{ri, k}]){		// 不满足：对所有的 D∈[j.d, i]，地区 j.r 都在风险范围内 
					flag=0;break;
				}
			}
			
			if(flag)
				u1.push_back({di,ui,ri});
		}
		 
		
		// 5. 输出答案
		set<int> ans;
		for(auto j:u1)	ans.insert(j.u);	// 只取用户，同时满足用户按顺序输出 
		cout<< i << ' ';
		for(auto j:ans)	cout<< j <<' ';
		cout<< '\n'; 
		 
	} 
	return 0;
}
```


