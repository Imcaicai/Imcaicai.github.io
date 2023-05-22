# 【CSP】202109题解


## 1 数组推导

🔗 **题目：[数组推导](http://118.190.20.162/view.page?gpid=T129)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,s1=0,s2=0,b[105]={0};
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
		scanf("%d",&b[i]);
	for(int i=n;i>=1;i--){
		s1+=b[i];
		if(b[i]>b[i-1])	s2+=b[i];	// a[i]=b[i] 
	}
	cout<<s1<<endl<<s2<<endl;
	return 0;
}
```

## 2 非零段划分

🔗 **题目：[非零段划分](http://118.190.20.162/view.page?gpid=T130)**

🔴 这个题目刚看上去有点难，需要找前后的递推关系。倒序遍历 a[n]，来确定 p 的值。用 set 和 map 会方便很多。

🔵 set 的倒序遍历：

```c++
	set<int>::reverse_iterator rit;
	for (rit=s.rbegin();rit!=s.rend();rit++){.....}
```

完整代码：

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,t,a[500005]={0},max=0;
	set<int> s;					// a[n]中元素的顺序排列 
	map<int,vector<int> > mp;	// 键：a[n]，值：在a[n]中出现过的所有下标
	scanf("%d",&n);
	for(int i=1;i<=n;i++){
		scanf("%d",&t);
		s.insert(t);
		mp[t].push_back(i);
	}
	t=0;										// 记录上一个p非零段的个数 
	set<int>::reverse_iterator rit;
	for (rit=s.rbegin();rit!=s.rend();rit++){	// 遍历每个元素	
		for(int k=0;k<mp[*rit].size();k++){		// 遍历该元素的每个位置 
			int i= mp[*rit][k]-1;				// 左边的元素下标 
			a[i+1]=1;
			while(mp[*rit][k+1]==mp[*rit][k]+1){	// 把一坨值一样的元素看做一个整体 
				a[mp[*rit][k+1]]=1;
				k++;
			}
			int j=mp[*rit][k]+1;					// 右边的元素下标 
            // 原来两边都是0或者在两端，另一边是0，则该处的a[]变成1后，非零段加一（分裂一段全是0的）
			if((i==0||a[i]==0)&&(j==n+1||a[j]==0)) t++;	
            // 原来两边都是1，则该处的a[]变成1后，非零段减一（合并两段非零段）
			else if(a[i]==1&&a[j]==1) t--;	 
		}
		if(t>=max) max=t;
	}
	cout<<max; 
	return 0;
}
```


