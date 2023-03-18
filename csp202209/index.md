# 【CSP】202209题解


## 2

错误的：

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入书本数量 n，包邮条件 x 
	int n,x;
	scanf("%d",&n);
	scanf("%d",&x);
	
	// 输入书本价格，计算总和 sum 
	int a[n];
	int sum=0;
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
		sum+=a[i];
	} 
	
	// 动态规划，找前 i 本书的价格和最大，不超过 sum-x 
	int d[n][sum-x+1]={0};
	for(int j=0;j<sum-x+1;j++){
		d[0][j]=a[0]>j ? 0 : a[0];
	}
	
	for(int i=1;i<n;i++){
		for(int j=0;j<sum-x+1;j++){
			if(d[i-1][j-a[i]]+a[i] <=j){
				d[i][j]=max(d[i-1][j-a[i]]+a[i],d[i-1][j]);
			}
			else d[i][j]=d[i-1][j];
			
		}
	}
	

	printf("\n%d",sum-d[n-1][sum-x]);
	
} 
```

正确的：

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入书本数量 n，包邮条件 x 
	int n,x;
	scanf("%d",&n);
	scanf("%d",&x);
	
	// 输入书本价格，计算总和 sum 
	int a[n];
	int sum=0;
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
		sum+=a[i];
	} 
	
	// 动态规划，找前 i 本书的价格和最大，不超过 sum-x 
	int d[n][sum-x+1]={0};
	for(int j=0;j<sum-x+1;j++){
		d[0][j]=a[0]>j ? 0 : a[0];
	}
	
	for(int i=1;i<n;i++){
		for(int j=0;j<sum-x+1;j++){
			if(a[i] <=j){
				d[i][j]=max(d[i-1][j-a[i]]+a[i],d[i-1][j]);
			}
			else d[i][j]=d[i-1][j];
			
		}
	}
	

	printf("%d",sum-d[n-1][sum-x]);
	
} 
```

## 3

```c++
#include <bits/stdc++.h>

using namespace std;

struct Node{			// 一条漫游数据 
	int d;
	int u;
	int r;
};

int n;
map<pair<int,int>,bool> mp;		// 键：地点+日期，值：是否是风险地区 
vector<Node> user;				// 存放有风险的用户漫游数据 

int main()
{
	ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
	cin >> n;					// 输入天数 n 
	for(int i=0;i<n;i++){
		int ri,mi;
		cin >> ri >> mi;		// 输入每天的风险地数量、漫游数据量 

		// 先clear
		// map<pair<int,int>,bool> tmp;
		for(map<pair<int,int>,bool>::iterator iter = mp.begin();iter!=mp.end();){
			map<pair<int,int>,bool>::iterator it1 = iter;
			iter++;
			if(i - ((it1->first).second) >= 7)	mp.erase(it1);	// 每次新的一天，检查是否有 7 天外的风险地区数据，有就删掉						
		}


		// 插入合法的
		for(int j=1;j<=ri;j++){						// 插入风险地区 
			int pij;
			cin >> pij;
			for(int date=i;date<i+7;date++){	
				mp[{pij,date}] = 1;					// 根据地区、时间标记风险地区 
			}
		}

		// 筛选不合法的								// 以前的风险用户 
		vector<Node> vc;
		for(auto t:user){							// 遍历漫游数据 
			bool f = 1;
			int r = t.r;
			if(i - t.d >= 7)	continue;			// 只看近 7 天的数据 

			for(int date = t.d;date<=i;date++){
				if(!mp.count({r,date})){			// 该地区和日期在风险范围内 
					f = 0;
					break;
				}
			}
			if(f)		vc.push_back(t);
		}
		user.clear();
		user = vc;									// 每一天都要更新风险用户列表 

		// 必须是七天以内收到的漫游数据				// 今天的风险用户 
		for(int j=1;j<=mi;j++){
			int d,u,r;
			cin >> d >> u >> r;

			if(i - d >= 7)	continue;				// 7 天外的数据就丢掉 
			// check 合法性
			// 从 d 到 i 天地区r均为风险地区即可
			bool f = 1;
			for(int k=d;k<=i;k++){
				if(!mp.count({r,k})){				// 在风险记录内，就加进来 
					f = 0;
					break;
				}
			}

			if(f)	user.push_back({d,u,r});
		}

		set<int> ans;
		for(auto x:user)	ans.insert(x.u);		// 只取用户 

		cout << i << ' ';
		for(auto x:ans){
			cout << x << ' ';
		}
		cout << '\n';
	}


	return 0;
}
```


