# 【CSP】202303题解


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

**【数据结构】**

🔴`map<int,vector<int>> mp0` 记录属性号及拥有该属性的所有用户下标

- 每个属性号对应一系列用户，很容易想到使用map，实现 `int` 到 `vector<int>` 的映射
- 在C++中嵌套多个STL类型容易出错，可以用 `typedef pair<int,int> P;` 简化

🟡 `map<P,vector<int>> mp1` 记录属性号和属性值，及拥有该组合的所有用户下标

- 使用 `auto &v:mp1[P(l,r)]` 在每个 vector 中遍历

🟢 `bitset<N>stk[N]` 记录每次计算结果的堆栈

- bitset 可以简化按位操作，特别是“与”、“或”
- 这种题目想到用堆栈，注意入栈、出栈时间！！！

🔵 `set<int> ans` 记录结果

- 因为题目要求用户编号由小到大输出，因此使用set结构存储

🟣 `char op[N]` 以堆栈形式记录依次出现的操作符

**【思路】**

✔ 首先是定义数据结构，实现输入（模拟题）

✔ 依次遍历匹配表达式，判断当前遍历到的字符，并决定好对应的操作，尤其是入栈出栈相关

✔ 实现断言与反断言，通过 bitset 得到结果，好巧妙的操作！只需要属性号对用户的映射、属性号+属性值对用户的映射就能完成。

✔ 最后不要忘记将用户下标转成编号，用 set 实现按序输出结果。（前面都用下标是因为，用户数的最大值已知，但其编号的范围未知）

```c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2600;
typedef pair<int,int> P; 
int n,id,k,x,y,len,m;			// 用户数、用户id、用户属性数、属性号、属性值、匹配表达式长度、匹配表达式行数 
int idx[N],num[N]; 				// 用户编号 
map<int,vector<int>> mp0;		// 键：属性号，值：有该属性的用户下标 
map<P,vector<int>> mp1;			// 键：属性号和属性值，值：有该属性号、属性值的用户下标 
char s[N],op[N];				// 每行匹配表达式、操作符数组 
bitset<N>stk[N];				// 记录结果的堆栈 
set<int> ans;					// 记录结果

// 断言与反断言 
bitset<N> ass(char k, int l, int r){
	bitset<N> res;
	for(auto &v:mp1[P(l,r)])	res.set(v);
	if(k=='~'){
		for(auto &v:mp0[l])	res.flip(v);
	}
	return res;
}

int main(){
	// 输入用户及属性信息 
	scanf("%d",&n);
	for(int i=0;i<n;i++){
		scanf("%d %d",&id,&k);
		idx[i] = id;				// 存储用户编号 
		for(int j=0;j<k;j++){
			scanf("%d %d",&x,&y);	// 属性号、属性值 
			mp0[x].push_back(i);
			mp1[P(x,y)].push_back(i); 
		} 
	} 

	// 判断m行匹配表达式 
	scanf("%d",&m);
	for(int i=0;i<m;i++){
		scanf("%s",s);
		len = strlen(s);
		
        // 遍历一行匹配表达式，分情况讨论正在遍历的字符
		int j=0,c=0,d=0;
		while(j<len){
			if(s[j]=='&'||s[j]=='|'){
				op[++c] = s[j++];		// 操作符入栈 
			}
			else if(s[j]=='('){
				j++;
			}
			else if(s[j]==')'){
				num[c]++;
				if(num[c]==2){
					d--;				// 相当于出栈，执行操作符
					if(op[c]=='&')	stk[d] = stk[d]&stk[d+1];
					else stk[d] = stk[d]|stk[d+1];
					num[c--]=0;
				}
				j++;
			} 
			else{
				int l=0,r=0;			// 左操作数、右操作数 
				while(j<len && s[j]!=':' && s[j]!='~'){	// 得到第一个操作数 
					l = l*10+(s[j]-'0');
					j++;
				}
				char k = s[j++]; 		// 得到操作符
				while(j<len && s[j]!=':' && s[j]!='~' && s[j]!=')'){	// 得到第二个操作数
					r = r*10+(s[j]-'0');
					j++;
				} 
				stk[++d] = ass(k,l,r);	// 执行断言与反断言
			}
		}
		
		// 输出答案
		for(int j=0;j<n;j++){			// 找出符合要求的用户编号
			if(stk[d].test(j))
				ans.insert(idx[j]);
		} 
		
		if(ans.empty())
			cout<<"\n";
		else{
			for(auto &v:ans)
				cout<<v<<" ";
			cout<<"\n";
		}
		
		ans.clear();	// 结束之后要清空！！！ 
	} 
}
```






