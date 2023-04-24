# 【CSP】202303题解


⏰ 时间：	🎯成绩：

| 题号 | 时间/min | 正确率 | 备注 |
| ---- | -------- | ------ | ---- |
| 1    | 20       | ✅      |      |
| 2    | 30       | ✅      |      |
| 3    |          |        |      |
| 4    |          |        |      |
| 5    |          |        |      |



## 1 田地丈量

🔗 **题目：[田地丈量](http://118.190.20.162/submitlist.page?gpid=T165)**

分情况求出长、宽，再计算总和即可。

```c++
#include <bits/stdc++.h>
using namespace std;

int main(){
	
	int n,a,b,x,y,x1,x2,y1,y2,sum=0;
	cin>>n>>a>>b;
	
	for(int i=0;i<n;i++){
		scanf("%d %d %d %d",&x1,&y1,&x2,&y2);
		if(x1<0 && x2>=0 && x2<=a)	x=x2;
		else if(x1<0 && x2>a)	x=a;
		else if(x1>=0 && x1<=a && x2>a)	x=a-x1;
		else if(x1>=0 && x1<=a && x2>=0 && x2<=a)	x=x2-x1;
		else continue;
		
		if(y1<0 && y2>=0 && y2<=b)	y=y2;
		else if(y1<0 && y2>b)	y=b;
		else if(y1>=0 && y1<=b && y2>b)	y=b-y1;
		else if(y1>=0 && y1<=b && y2>=0 && y2<=b)	y=y2-y1;
		else continue;
		
		sum+=x*y;
	} 
	
	cout<<sum;
	return 0;
} 
```

## 2 垦田计划

🔗 **题目：[垦田计划](http://118.190.20.162/view.page?gpid=T164)**

```c++
#include <bits/stdc++.h>
using namespace std;

int main(){
	
	int n,m,k,t,c,ans=0;
	static int a[100005]={0};
	cin>>n>>m>>k;
	
	for(int i=0;i<n;i++){
		scanf("%d %d",&t,&c);
		if(t>ans)	ans=t;		// 记录最大天数 
		a[t-1]+=c;				// 所有天数为t的田地减少一天需要的资源数 
	} 
	
	int sum = a[ans-1];			// 把所有大于当前天数的田地都减少一天的资源数 
	while(m>0 && ans>k){
		if(m>sum){
			m-=sum;
			ans--;				// 答案从最大天数开始慢慢减少
			sum+=a[ans-1];		// 累加所需资源数 
		}
		else break;
	}
	
	if(ans>k)	cout<<ans;		// 注意最终ans不能大于k 
	else	cout<<k;

	return 0;
} 
```




