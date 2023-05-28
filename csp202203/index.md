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

## 3 计算资源调度器

🔗 **题目：[计算资源调度器](http://118.190.20.162/view.page?gpid=T141)**

```c++
#include<bits/stdc++.h>
using namespace std;

struct Node{
	int id;				// 节点编号 
	int block;			// 可用区编号 
	int num;			// 任务数 
	set<int> task;		// 节点上的任务列表 
	bool operator <(const Node &a)const{
		if(this->num!=a.num) 
			return this->num<a.num;
		else return this->id<a.id;
	}
};
int n,m,t,g,f,a,na,pa,paa,paar; 
Node node[1005];				// 保存所有节点 
vector<int> temp;				// 保存所有节点编号 
map<int,vector<int> > mp1;		// 键：可用区编号，值：在该可用区的节点 

int main(){
	// 输入 
	cin>>n>>m;							// 计算节点数目、可用区数目
	for(int i=1;i<=n;i++){
		node[i].id=i;
		scanf("%d",&node[i].block);		// 可用区编号
		mp1[node[i].block].push_back(i);// 可用区和节点配对 
		temp.push_back(i); 				// 保存所有节点的编号 
	}
	scanf("%d",&g);
	
	// 分配计算任务 
	while(g--){
		scanf("%d %d %d %d %d %d",&f,&a,&na,&pa,&paa,&paar);
		while(f--){
			vector<int> t1=temp,t2; 
			if(na>0){	// 节点亲和性要求 
				t1=mp1[na];
			}
			if(pa>0){	// 计算任务亲和性要求 
				set<int> idx;
				for(int i=1;i<=n;i++){	// 得出编号为pa的应用的可用区编号 
					if(node[i].task.count(pa)){
						idx.insert(node[i].block); 	// 注意一个应用可能在多个可用区上！！！ 
					}
				}
				for(auto &v:t1){
					if(idx.count(node[v].block))
						t2.push_back(v);
				}
				t1=t2;t2.clear();
			}
			if(paa>0){	// 计算任务的反亲和性要求 
				for(auto &v:t1){
					if(node[v].task.count(paa)) continue;
					else t2.push_back(v);
				}
			}
			if(!t2.empty() || paar==1) t1=t2;
			if(t1.empty()) printf("0 ");
			else{
				// 注意这种排序方式！！！ 
				sort(t1.begin(),t1.end(),[](const auto &a,const auto &b)->bool{
					return node[a]<node[b];});
				// 更新节点信息 
				node[t1[0]].num++;
				node[t1[0]].task.insert(a); 
				printf("%d ",t1[0]);
			}
		}
		printf("\n");
	}
	return 0;
} 
```


