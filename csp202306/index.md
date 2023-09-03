# 【CSP】202303题解


## 1 重复局面

🔗 **题目：[重复局面](http://118.190.20.162/submitlist.page?gpid=T170)**

将一盘局的局面表示为一个长度为64的字符串。

建立map映射，key为字符串表示的局面，value为出现的次数。

```c++
#include<bits/stdc++.h>
using namespace std;

map<string,int> mp;
int n;

int main(){
	cin>>n;
	for(int i=0;i<n;i++){
		string s="",tmp;
		for(int j=0;j<8;j++){
			cin>>tmp;
			s = s+tmp;
		}
		mp[s] += 1;
		cout<<mp[s]<<endl;
	}
}
```

## 2 矩阵运算

🔗 **题目：[矩阵运算](http://118.190.20.162/view.page?gpid=T169)**

改变矩阵运算的顺序，可以减中间结果的存储空间和运行时间。

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,d;
	cin>>n>>d;
	int q[n][d],k[d][n],v[n][d],w[n]; 
	long long tmp[d][d],ans,a,b,c;
	memset(tmp,0,sizeof(tmp));
 
	for(int i=0;i<n;i++)		// 输入q 
		for(int j=0;j<d;j++)
			cin>>q[i][j];
	for(int i=0;i<n;i++)		// 输入k的转置 
		for(int j=0;j<d;j++)
			cin>>k[j][i];
	for(int i=0;i<n;i++)		// 输入v
		for(int j=0;j<d;j++)
			cin>>v[i][j];
	for(int i=0;i<n;i++)		// 输入w
		cin>>w[i];
		
	for(int i=0;i<d;i++)
		for(int j=0;j<d;j++)
			for(int t=0;t<n;t++)
				tmp[i][j] += k[i][t]*v[t][j];

	for(int i=0;i<n;i++){		//  计算最终结果 
		for(int j=0;j<d;j++){
			ans=0;
			for(int t=0;t<d;t++){
				ans += (long long)w[i]*q[i][t]*tmp[t][j];
			}
			cout<<ans<<" ";
		}
		cout<<endl;
	}
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






