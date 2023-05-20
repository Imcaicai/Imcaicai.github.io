# 【CSP】202203题解


## 1 未初始化警告

🔗 **题目：[未初始化警告](http://118.190.20.162/view.page?gpid=T143)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,k,cnt=0,x[100005],y[100005],idx[100005]={0};
	cin>>n>>k;
	for(int i=1;i<=k;i++){
		scanf("%d %d",&x[i],&y[i]);
		if(idx[x[i]]==0)	idx[x[i]]=i;
	}
	for(int i=1;i<=k;i++){
		if(y[i]!=0 && (idx[y[i]]>=i || idx[y[i]]==0))
			cnt++;
	}
	cout<<cnt;
	return 0;
}
```

## 2 出行计划

🔗 **题目：[出行计划](http://118.190.20.162/view.page?gpid=T142)**

险些超时（

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,m,k,q,t,c,a[200005]={0};
	scanf("%d %d %d",&n,&m,&k);		// 出行计划数目、查询个数、等待结果所需时间
	for(int i=0;i<n;i++){
		scanf("%d %d",&t,&c);	// 进入时间、所需多久内的核酸检测结果 
		t -= k;c = t-c+1;
		int j=max(0,c);
		while(j<=t){
			a[j]++;j++;
		}
	}
	for(int i=0;i<m;i++){
		scanf("%d",&q);
		printf("%d\n",a[q]);
	} 
	
	return 0;
}
```


