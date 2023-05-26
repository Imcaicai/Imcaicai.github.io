# 【CSP】202006题解


## 1 线性分类器

🔗 **题目：[线性分类器](http://118.190.20.162/view.page?gpid=T105)**

```c++
#include<bits/stdc++.h>
using namespace std;

struct node{
	int x,y;
};
int main(){
	int n,m,a,b,c,x,y,s;
	vector<node> v1,v2;
	char t;
	cin>>n>>m;
	for(int i=0;i<n;i++){
		cin>>x>>y>>t;
		if(t=='A') v1.push_back({x,y});
		else v2.push_back({x,y});
	}
	for(int i=0;i<m;i++){
		int flag=1,f1=0,f2=0;
		cin>>a>>b>>c;
		for(auto &v:v1){
			s=a+b*v.x+c*v.y;
			if(f1==0){
				if(s<0) f1=1;
				else f1=2;
			}
			else if((s<0&&f1==2)||(s>0&&f1==1)){
				flag=0;break;
			}
		}
		if(flag==0){
			cout<<"No"<<endl;continue;
		}
		for(auto &v:v2){
			s=a+b*v.x+c*v.y;
			if(f2==0){
				if(s<0) f2=1;
				else f2=2;
			}
			else if((s<0&&f2==2)||(s>0&&f2==1)){
				flag=0;break;
			}
		}
		if(flag==0) cout<<"No"<<endl;
		else cout<<"Yes"<<endl;
	}
	return 0;
} 
```



## 2 稀疏矩阵

🔗 **题目：[稀疏矩阵](http://118.190.20.162/view.page?gpid=T104)**

```c++
#include<bits/stdc++.h>
using namespace std;

int n,a,b,x,y;
long long ans=0;
map<int,int> mp1,mp2;

int main(){
	scanf("%d %d %d",&n,&a,&b);
	for(int i=0;i<a;i++){
		scanf("%d %d",&x,&y);
		mp1[x]=y;
	}
	for(int i=0;i<b;i++){
		scanf("%d %d",&x,&y);
		mp2[x]=y;
	}
	for(auto &v:mp1){
		if(mp2.count(v.first)) 
			ans+=(long long)(v.second*mp2[v.first]);
	}
	printf("%lld",ans);
	
	return 0;
}
```


