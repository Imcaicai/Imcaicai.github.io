# 【CSP】202009题解


## 1 称检测点查询

🔗 **题目：[称检测点查询](http://118.190.20.162/view.page?gpid=T113)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){	 
	int n,x,y,a,b,d,ans[3][2];
	scanf("%d %d %d",&n,&x,&y);
	ans[0][0]=ans[1][0]=ans[2][0]=100000000;
	for(int i=1;i<=n;i++){
		scanf("%d %d",&a,&b);
		d=(x-a)*(x-a)+(y-b)*(y-b);
		if(d>=ans[0][0]) continue;
		else if(d>=ans[1][0]){
			ans[0][0]=d,ans[0][1]=i;
		}
		else if(d>=ans[2][0]){
			ans[0][0]=ans[1][0],ans[0][1]=ans[1][1];
			ans[1][0]=d,ans[1][1]=i;
		}
		else{
			ans[0][0]=ans[1][0],ans[0][1]=ans[1][1];
			ans[1][0]=ans[2][0],ans[1][1]=ans[2][1];
			ans[2][0]=d,ans[2][1]=i;
		}
	}
	printf("%d\n%d\n%d",ans[2][1],ans[1][1],ans[0][1]);
	return 0;
}
```

## 1 风险人群筛查

🔗 **题目：[风险人群筛查](http://118.190.20.162/view.page?gpid=T112)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,k,t,xl,yd,xr,yu,x,y,c1=0,c2=0;
	cin>>n>>k>>t>>xl>>yd>>xr>>yu;
	for(int i=0;i<n;i++){
		int p=0,f1=0,f2=0;
		for(int j=0;j<t;j++){
			scanf("%d %d",&x,&y);
			if(x>=xl&&x<=xr&&y>=yd&&y<=yu)
				f1=1,p++;
			else p=0;
			if(p>=k) f2=1;
		}
		if(f2) c1++,c2++;
		else if(f1) c1++;
	}
	cout<<c1<<endl<<c2<<endl;
	return 0;
}
```


