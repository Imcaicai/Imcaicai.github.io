# 【CSP】202112题解


## 1 序列查询

🔗 **题目：[序列查询](http://118.190.20.162/view.page?gpid=T138)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,m,a1=0,a2,sum=0;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++){
		scanf("%d",&a2);
		sum += (a2-a1)*(i-1);
		a1 = a2;
	}
	sum += (m-a2)*n;
	printf("%d",sum);
	return 0;
} 
```

## 2 序列查询新解

🔗 **题目：[序列查询新解](http://118.190.20.162/view.page?gpid=T137)**

🔴 注意数据范围：`sum` 是 `long long` 型的

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,m,a1=0,a2,g,t;
	long long sum = 0;
	scanf("%d %d",&n,&m);
	int r = m/(n+1);
	for(int i=1;i<=n;i++){
		scanf("%d",&a2);
		for(int j=a1;j<=a2-1;j++){
			g = j/r;
			t = min((g+1)*r-1,a2-1);				// g=j/r
			sum += (long long)(t-j+1)*abs(g-i+1);	// f(x)=i-1 
			j = t;
		}
		a1 = a2;
	}
	for(int j=a1;j<=m-1;j++){
		g = j/r;
		t = min((g+1)*r-1,m-1);				// g=j/r
		sum += (long long)(t-j+1)*abs(g-n);	// f(x)=i-1 
		j = t;
	}
	cout<<sum;
	return 0;
} 
```

## 3 登机牌条码

🔗 **题目：[登机牌条码](http://118.190.20.162/view.page?gpid=T136)**

🔴 注意审题！bug调不出来最好重读一遍题目！这里开始编码时，编码器处于大写状态！！！

🟡 计算多项式 $g(x)=(x-3)(x-3^2)...(x-3^k)$ 中x的系数：找递推关系

🔵 模拟多项式除法

```c++
#include<bits/stdc++.h>
using namespace std;

int w,s,k=0,t,len,mi=-3,type[2]={0},n;	 
char ym[2000];	 
vector<int> d,d1,g;					// 数据码字
 
void getType(char c){				// 获取字符的类别 
	if(c>='A'&&c<='Z') 
		type[0]=0,type[1]=c-'A';	// 大写字母，0
	else if(c>='a'&&c<='z') 
		type[0]=1,type[1]=c-'a';	// 小写字母，1
	else type[0]=2,type[1]=c-'0';	// 数字，2 
}

void getYxsj(){						// 获取有效数据 
	for(int i=0;i<len;i++){
		if(type[0]==0){								// 上一个是大写
			getType(ym[i]); 
			if(type[0]==1) d.push_back(27);			// 大写-小写 
			else if(type[0]==2) d.push_back(28);	// 大写-数字 
		}
		else if(type[0]==1){						// 上一个是小写
			getType(ym[i]); 
			if(type[0]==0)	
				d.push_back(28),d.push_back(28);	// 小写-大写 
			else if(type[0]==2)	d.push_back(28);	// 小写-数字 
		} 
		else{										// 上一个是数字 
			getType(ym[i]); 
			if(type[0]==0) d.push_back(28);			// 数字-大写 
			else if(type[0]==1)	d.push_back(27);	// 数字-小写			
		}
		d.push_back(type[1]);
	}
	if(d.size()%2==1) d.push_back(29);				// 奇数个数字，补29
	for(int i=0;i<d.size();i+=2)
		d1.push_back(30*d[i]+d[i+1]);				// 有效数据码字放到d1中 
	
	d.clear();d.push_back(0);
	for(auto &v:d1) d.push_back(v);
	t=d1.size()+1+k;
	while(t%w) d.push_back(900),t++;				// 填充码字
	d[0] = t-k;n=t-k;								// 数据码字长度 
	for(auto &v:d) printf("%d\n",v);				// 输出有效数据码字 
}

int main(){
	// 输入 
	scanf("%d %d",&w,&s);	// 每行码字数、校验级别
	scanf("%s",ym);			// 原始输入字符
	if(s!=-1) k=1<<(s+1);	// 校验字数目 
	len = strlen(ym);
	
	getYxsj(); 				// 获取数据码字 
	if(s==-1) return 0;		// 无校验码字 
	
	// 计算 g 的系数，倒序
	g.push_back(1);g.push_back(-3);
	for(int i=2;i<=k;i++){				 
		mi = mi*3%929;
		g.push_back(0);
		for(int j=g.size()-1;j>=1;j--){
			g[j]+=mi*g[j-1]%929;g[j]%=929;
		}
	}

	// 模拟多项式除法
	for(int i=1;i<=k;i++) d.push_back(0);	
	for(int i=0;i<n;i++){				 
		t=d[i]; 
		for(int j=i+1;j<=i+k;j++){
			d[j] -= t*g[j-i]%929;d[j]%=929;
		}
	}
	
	// 输出结果 
	for(int i=n;i<n+k;i++){
		t=(-d[i])%929;
		if(t<0)	t+=929;
		cout<<t<<endl;
	}
	return 0;
}
```


