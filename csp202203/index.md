# 【CSP】202203题解


## 1

错误的：

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入变量个数 n，语句个数 k
	int n,k;
	scanf("%d",&n);
	scanf("%d",&k);
	
	// 输入语句
	int x[n+1]={0},y[n+1]={0},a[n+1]={0};
	for(int i=1;i<=k;i++){
		scanf("%d",&x[i]);
		scanf("%d",&y[i]);
	} 
	
	for(int i=1;i<=k;i++){
		if(a[x[i]]==0){
			a[x[i]]=i;
		}
	}
	
	// 记录不符合的语句个数
	int cnt=0;
	for(int i=1;i<=k;i++){
		if(y[i]!=0 && i<=a[y[i]]){
			cnt++;
		}
	} 
	
	printf("%d",cnt);
	
	
} 
```

正确的：

```c++
// 需要用 CPP11 或 CPP14 
// #include<bits/stdc++.h>
#include <iostream>
using namespace std;
 
int main(){
	
	// 输入变量数量 n，赋值语句 k
	int n,k;
	scanf("%d %d",&n,&k);	// 用 cin 可能超时，改为 scanf 
	
	// 输入 k 行语句 x[i] y[i]
	int x[k],y[k];
	for(int i=0;i<k;i++){
		scanf("%d %d",&x[i],&y[i]);
	} 
	
	// 判断有无不符合赋值规则的
	int cnt=0;
	for(int i=0;i<k;i++){
		int flag=1;
		if(y[i]==0)
			continue;
		for(int j=0;j<i;j++){
			if(x[j]==y[i]){
				flag=0;
				break;
			}
		}
		
		cnt += flag;
	} 
	
	cout<<cnt;
}
```



## 2

```c++
// 需要用 CPP11 或 CPP14 
// #include<bits/stdc++.h>
#include <iostream>
using namespace std;
 
int main(){
	
	// 输入计划数 n，查询数 m，等核酸天数 k 
	int n,m,k;
	scanf("%d %d %d",&n,&m,&k);
	
	// 输入 t[i] c[i]
	int t,c;
	// 错误原因：1. 数组开的不够大 2. 数组没有初始化 
	int r[200005]={0};
	for(int i=0;i<n;i++){
		scanf("%d %d",&t,&c);
		if(t-k<1)
			continue;
		int temp=max(1,t-k-c+1);
		for(int j=temp;j<=t-k;j++){
			r[j]++;
		} 
	} 
	
	// 输入 m 个 q[i]
	int q;
	for(int i=0;i<m;i++){
		int cnt=0;
		scanf("%d",&q);
		
		printf("%d\n",r[q]);
	} 
	
	
}
```


