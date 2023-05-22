# 【CSP】202012题解


## 1 期末预测之安全指数

🔗 **题目：[期末预测之安全指数](http://118.190.20.162/view.page?gpid=T123)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,w,s,y=0;
	scanf("%d",&n);
	for(int i=0;i<n;i++){
		scanf("%d %d",&w,&s);
		y += w*s;
	}
	if(y>0)	cout<<y;
	else cout<<0;
	return 0;
}
```

## 2 期末预测之最佳阈值

🔗 **题目：[期末预测之最佳阈值](http://118.190.20.162/view.page?gpid=T122)**

用 `set` 和 `map` 会方便很多。要找递推关系！！！

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int m,y,r,max,idx,t1=0,t2,v1;
	map<int,int> mp0,mp1;
	set<int> c;
	scanf("%d",&m);
	for(int i=1;i<=m;i++){
		scanf("%d %d",&y,&r);
		c.insert(y);
		if(r==0) mp0[y]++;		// 记录结果为0的个数 
		else t1++,mp1[y]++;		// 记录结果为1的个数 
	}
	int i=0;max = t1,idx=0;
	for(auto &v:c){
		if(i==0){				// 第一个元素已知 
			v1=v,i++;continue;
		}
		t2 = t1+mp0[v1]-mp1[v1];
		if(t2>=max)	max=t2,idx=v;
		t1 = t2,v1=v;
	}
	cout<<idx;
	return 0;
}
```


