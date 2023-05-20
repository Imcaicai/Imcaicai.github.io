# 【CSP】202206题解


## 1 归一化处理

🔗 **题目：[归一化处理](http://118.190.20.162/view.page?gpid=T148)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,a[1005];
	double avg=0,d=0,f;
	scanf("%d",&n);
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
		avg += a[i];
	}
	avg /= n;	
	for(int i=0;i<n;i++)
		d += (a[i]-avg)*(a[i]-avg);
	d /= n;
	d = pow(d,0.5);
	for(int i=0;i<n;i++){
		f = (a[i]-avg)/d;
		printf("%.16f\n",f);
	}
	
	return 0;
}
```

## 2 寻宝！大冒险！

🔗 **题目：[寻宝！大冒险！](http://118.190.20.162/view.page?gpid=T147)**

🔴 注意：题目中所给的一系列坐标 x[n]、y[n] 不一定是顺序的！！！

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	// 输入 
	int n,l,s,cnt=0,x[1005],y[1005],b[60][60];	// 棵树、绿化图、藏宝图大小、计数器 
	scanf("%d %d %d",&n,&l,&s); 
	for(int i=0;i<n;i++)
		scanf("%d %d",&x[i],&y[i]);
	for(int i=s;i>=0;i--)
		for(int j=0;j<=s;j++)
			scanf("%d",&b[i][j]);
	
	for(int k=0;k<n;k++){
		int flag=1;
		if(x[k]+s>l || y[k]+s>l)	continue;	// 判断坐标是否溢出 
		int a[60][60]={0};						// 构造大小(s+1)*(s+1)的矩阵a
		for(int i=0;i<n;i++){
			// 注意这里 x 和 y 不一定是从小到大排列的，不要想当然！！！ 
			if(x[i]-x[k]>=0 && y[i]-y[k]>=0 && x[i]-x[k]<=s && y[i]-y[k]<=s)
				a[x[i]-x[k]][y[i]-y[k]] = 1;
		}
		
		for(int i=0;i<=s;i++){
			for(int j=0;j<=s;j++)
				if(a[i][j]!=b[i][j])	flag=0;
		}
		cnt += flag;
	}

	cout<<cnt;
	return 0;
} 
```

## 3 角色授权

🔗 **题目：[角色授权](http://118.190.20.162/view.page?gpid=T146)**

🔴 **数据结构**

没什么很难的，都是常见的STL容器组装。

- `ROLE`  ：角色（包含角色名称、操作清单、资源种类、资源名称）
- `map<string,vector<int> > ump` ：用户名及其关联的角色下标
- `map<string,vector<int> > zmp` ：用户组名及其关联的角色下标
- `map<string,int> rmp` ：角色名称到其下标的映射

🟡 **输入输出**

- 用 scanf/printf 容易有奇奇怪怪的错误，可以用 cin/cout
- cin/cout 加速：`ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);` ，此时不可以再用 scanf/printf

🟢 vector 查找元素：find(v.begin(),v.end(),x) 和 v.end() 相比较

🔵 用set查找元素更快！！！本来ROLE中用的是set而不是vector，测评的时候可能90（超时），也可能100；改成set速度会快很多。

```c++
#include<bits/stdc++.h>
#include<iostream>
using namespace std;

struct ROLE{
	string name;		// 角色名称 
	set<string> op;		// 操作清单 
	set<string> ret;	// 资源种类
	set<string> ren;	// 资源名称 
};
ROLE role[500];			// 记录所有角色 
map<string,vector<int> > ump;	// 键：用户名，值：角色下标 
map<string,vector<int> > zmp;	// 键：用户组名，值：角色下标 
map<string,int> rmp;			// 键：角色名称，值：角色在role中的下标 
int n,m,q,nv,no,nn,ns,ng;
string s,t,x,y,z;
char c;

bool isfind(int v){				// 下标为v的角色是否满足授权行为的要求 
	if(role[v].op.count("*")==0 && role[v].op.count(x)==0)
		return false;
	else if(role[v].ret.count("*")==0 && role[v].ret.count(y)==0)
		return false;
	else if(role[v].ren.empty()==false && role[v].ren.count(z)==0)
		return false;
	else	return true;
}

void query(string u,int ng){			// 查询用户u是否满足授权行为的要求 
	vector<string> group;
	for(int i=0;i<ng;i++){				// 输入用户所属的用户组 
		cin>>t;group.push_back(t);
	}
	cin>>x>>y>>z;						// 操作名称、资源种类、资源名称 
	for(auto &v:ump[u]){				// 先判断用户自己是否有 
		if(isfind(v)){
			cout<<1<<endl;return;
		}
	}
	for(int i=0;i<group.size();i++){	// 再判断用户组是否有 
		for(auto &v:zmp[group[i]]){
			if(isfind(v)){
				cout<<1<<endl;return;
			}
		}
	} 
	cout<<0<<endl;return;
}

int main(){
	ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m>>q;						// 角色数量、角色关联数量、待检查操作数 
	for(int i=0;i<n;i++){				// 输入角色 
		cin>>role[i].name;				// 角色名
		rmp[role[i].name] = i; 
		cin>>nv;						// 操作数量
		for(int j=0;j<nv;j++){
			cin>>s;
			role[i].op.insert(s);
		} 
		cin>>no;						// 资源种类数量
		for(int j=0;j<no;j++){
			cin>>s;
			role[i].ret.insert(s);
		} 
		cin>>nn;						// 资源名称数量
		for(int j=0;j<nn;j++){
			cin>>s;
			role[i].ren.insert(s);
		} 			 
	}
	
	for(int i=0;i<m;i++){				// 输入角色关联
		cin>>s>>ns;							// 角色名称、授权对象的数量 
		int idx = rmp[s];				// 角色在role中的下标 
		for(int j=0;j<ns;j++){
			cin>>c>>t;						// 授权对象：用户/用户组、用户/用户组名称 
			if(c=='u')	ump[t].push_back(idx);
			else zmp[t].push_back(idx); 
		} 
	} 
	
	for(int i=0;i<q;i++){				// 待授权的行为 
		cin>>s>>ng;						// 用户名称、用户组的数量 
		query(s,ng);
	}
	
	return 0;
}
```










