# 【CSP】202206题解


## 1

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入 n，a，计算总和 sum 
	int n,sum=0;
	scanf("%d",&n);
	int a[n];
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
		sum+=a[i];
	} 
	
	// 计算平均数 x
	double x=(double)sum/n;
	// 计算方差 d
	double d=0;
	for(int i=0;i<n;i++){
		d=d+(a[i]-x)*(a[i]-x);
	} 
	d=d/n;
	
	// 计算 f
	double f[n];
	for(int i=0;i<n;i++){
		f[i]=(a[i]-x)/pow(d,0.5);
		printf("%.16f\n",f[i]);
	} 
	
	
} 
```

## 2

```c++
#include <iostream>
#include <cmath>

using namespace std;


int main()
{

	// 输入变量
	int n,l,s;
	scanf("%d %d %d",&n,&l,&s);
	int b[s+1][s+1];
	
	// 输入树的位置 
	int x[n],y[n];
	for(int i=0;i<n;i++){
		scanf("%d %d",&x[i],&y[i]);
	} 
	
	// 输入矩阵 S 
	for(int i=s;i>=0;i--){
		for(int j=0;j<=s;j++){
			scanf("%d",&b[i][j]); 
		}
	}
	
	int cnt=0;
	for(int i=0;i<n;i++){
		
		// 考虑树为起点构成的数组不能超过矩阵 L
		if(x[i]+s>l || y[i]+s>l){
			continue;	// 救命！这里用 continue，不是 break 
		} 
		
		// 构建新的矩阵 a 
		int a[60][60]={0};
		int flag=1;
		for(int j=0;j<n;j++){
			if(x[j]>=x[i] && x[j]<=x[i]+s && y[j]>=y[i] && y[j]<=y[i]+s){
				a[x[j]-x[i]][y[j]-y[i]]=1;
			} 
		}
		
		for(int j=0;j<=s;j++){
			for(int k=0;k<=s;k++){
				if(a[j][k]!=b[j][k]){
					flag=0;
					break;
				}
			}
			
			if(flag==0)
				break;
		} 
		
		cnt += flag;
	} 
	
	printf("%d",cnt);
	
	
} 
```

## 3

```c++
#include<bits/stdc++.h>
using namespace std;
 
class role{
	public:
		set<string> opl;
		set<string> opt;
		set<string> opn;
};
 
class group{
	public:
		set<string> rol;	// 关联内的用户组 
};
 
map<string,role> mpr;	// 角色：键为角色名，值为角色的操作名、资源种类、资源名 
map<string,group> mpg;	// 关联：键为角色名，值为一组用户组名 
 
class user{
	public:
		set<string> rol;	// 用户所关联的角色 
		set<string> grp;	// 用户所在的用户组 
		
		// 查看操作、资源种类、资源名是否能被用户使用 
		bool check(string opl,string opt,string opn){
			// 遍历用户所关联的角色
			for(auto it:rol)	 
				if(mpr[it].opl.count("*") || mpr[it].opl.count(opl))	// 操作是否符合 
					if(mpr[it].opt.count("*") || mpr[it].opt.count(opt))	// 资源种类是否符合 
						if(mpr[it].opn.empty() || mpr[it].opn.count(opn))	// 资源名是否符合 
							return true;
			
			// 遍历用户所在的用户组所关联的角色 
			for(auto it:grp)
				if(mpg.count(it))	// 已关联的用户是否包含用户所在的用户组 
					for(auto it1:mpg[it].rol)	// 遍历用户组所关联的角色 
						if(mpr[it1].opl.count("*") || mpr[it1].opl.count(opl))
							if(mpr[it1].opt.count("*") || mpr[it1].opt.count(opt))
								if(mpr[it1].opn.empty() || mpr[it1].opn.count(opn))
									return true;
			return false;
		}
		
};
map<string,user> mpu;	// 关联：键为角色名，值为用户名 
 
signed main(){
	//提高cin,cout的速度 
	ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	 
	int n,m,q,nv,no,nn,k;
	string name,rname,uname,x,y,z,ch;	
	cin>>n>>m>>q;	// 输入角色数 n，角色关联数 m，操作数 q 
	
	for(int i=0;i<n;i++){
		cin>>name>>nv;	// 可以直接用 cin，不用标准输入 
		while(nv--)
			cin>>x,mpr[name].opl.emplace(x);	// 输入操作名 
		cin>>no;
		while(no--)
			cin>>x,mpr[name].opt.emplace(x);	// 输入资源种类 
		cin>>nn;
		while(nn--)
			cin>>x,mpr[name].opn.emplace(x);	// 输入资源名 
	}
	
	for(int i=0;i<m;i++){
		cin>>rname>>k;
		for(int j=0;j<k;j++){
			cin>>ch>>name;
			if(ch == "g")
				mpg[name].rol.emplace(rname);	// 在【用户组】中加入关联：角色名+用户组名 
			else
				mpu[name].rol.emplace(rname);	// 在【用户】中加入关联：角色名+用户名 
		}
	}
	
	for(int i=0;i<q;i++){
		cin>>uname>>k;
		for(int j=0;j<k;j++)
			cin>>name,mpu[uname].grp.emplace(name);	// 输入用户所在的用户组 
		cin>>x>>y>>z;
		cout<<mpu[uname].check(x,y,z)<<endl;
		mpu[uname].grp.clear();	// 每次要清空用户所在的用户组，role 不用清空！！！ 
	}
	 
}
```


