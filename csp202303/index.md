# 【CSP】202303题解


⏰ 时间：	🎯成绩：

| 题号 | 时间/min | 正确率 | 备注 |
| ---- | -------- | ------ | ---- |
| 1    | 20       | ✅      |      |
| 2    | 30       | ✅      |      |
| 3    | 40       | ❌      |      |
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



## 3 LADP



```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int,int> P;	// 注意这种写法！！！ 
const int N=2502;
int n,m,sz,id,k,c,d,x,y,num[N],to[N],f[N];
map<int,int>q[N];
map<P,vector<int>>p;
map<int,vector<int>>has;
char s[N],op[N];			// 放匹配表达式和操作符 
bitset<N>stk[N*2],res;		// 堆栈可以直接用数组形式 

// 断言、反断言 
bitset<N>cal(int l,char x,int r){
	bitset<N>ans;
	for(auto &v:p[P(l,r)]){	
		ans.set(v);		// 如果是断言，符合的用户就是 p 
	}
	if(x=='~'){			// 如果是反断言，符合的用户为 has-p 
		for(auto &v:has[l]){
			ans.flip(v);	// 这也太巧妙了！！！	
		}
	}
	return ans;
}

int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%d%d",&id,&k);
		to[i]=id;					// 记录用户下标对应的编号 
		for(int j=1;j<=k;++j){
			scanf("%d%d",&x,&y);
			q[i][x]=y;				// 无用 
			has[x].push_back(i);	// 表示属性号为x的有哪些用户 
			p[P(x,y)].push_back(i);	// 属性号为x、属性值为y的有哪些用户 
		}
	}
	
	scanf("%d",&m);
	for(int i=1;i<=m;++i){
		scanf("%s",s);
		sz=strlen(s);				// 注意用 strlen 而不是 length() 
		c=d=0;
		for(int j=0;j<sz;){
			if(s[j]=='&' || s[j]=='|'){
				op[++c]=s[j++];		// op数组记录操作符，c为当前操作符下标 
			}
			else if(s[j]=='('){
				j++;
			}
			else if(s[j]==')'){
				num[c]++;			// 当前操作符的操作数个数加一 
				if(num[c]==2){		// 集齐2个操作数 
					d--;			// 相当于出栈，d表示当前已经计算出的结果个数 
					if(op[c]=='&')stk[d]=stk[d]&stk[d+1];
					else stk[d]=stk[d]|stk[d+1];
					num[c--]=0;		// 相当于出栈 
				}
				j++;
			}
			else{
				int cur=j,l=0,r=0;
				while(cur<sz && (s[cur]!=':' && s[cur]!='~')){	// 获取第一个数 
					l=l*10+(s[cur]-'0');
					cur++;
				}
				char x=s[cur++];				// 获取操作符 
				while(cur<sz && s[cur]!=')'){	// 获取第二个数 
					r=r*10+(s[cur]-'0');
					cur++;
				}
				stk[++d]=cal(l,x,r);			// 断言、反断言 
				j=cur;
			}
		}
		int e=0;
		for(int j=1;j<=n;++j){
			if(stk[d].test(j)){					// 是否设置了第j位 
				f[++e]=to[j];					// 用户下标转化为编号 
			}
		}
		sort(f+1,f+e+1);						
		for(int j=1;j<=e;++j){
			printf("%d%c",f[j]," \n"[j==e]);
		}
		if(!e)puts("");
	}
	return 0;
}
```






