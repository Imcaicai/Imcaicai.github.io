# 【CSP】202212题解


## 1 现值计算

🔗 **题目：[现值计算](http://118.190.20.162/view.page?gpid=T160)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){

	double i,ans,s;
	int n,t;
	cin>>n>>i>>ans;
	i+=1;s=i;
	for(int j=1;j<=n;j++){
		scanf("%d",&t);
		ans += t*1.0/s;
		s*=i;
	}
	cout<<ans;
	return 0;
}
```

## 2 训练计划

🔗 **题目：[训练计划](http://118.190.20.162/view.page?gpid=T159)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	// 输入 
	int n,m,mt=0;
	int p[105]={0},t[105]={0},b[105]={0},e[105]={0},f[105]={0};
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;i++){
		scanf("%d",&p[i]);
		f[p[i]]=1;
	}
	
	// 计算最早开始时间 
	for(int i=1;i<=m;i++){
		scanf("%d",&t[i]);
		b[i] = e[p[i]]+1;
		e[i] = b[i]+t[i]-1;
		if(e[i]>mt)	mt=e[i];
	}
	for(int i=1;i<=m;i++)
		printf("%d ",b[i]);
		
	// 如果不能在规定时间完成，退出
	if(mt>n)	return 0;
	// 否则，继续计算并输出最晚开始时间 
	fill(e,e+m+1,400);
	for(int i=m;i>=1;i--){
		if(f[i]!=1)
			b[i] = n-t[i]+1;
		else b[i] = e[i]-t[i]+1;	
		e[p[i]] = min(e[p[i]],b[i]-1);
	}
	printf("\n");
	for(int i=1;i<=m;i++)
		printf("%d ",b[i]);
	
	return 0;
} 
```

## 3 JPEG解码

🔗 **题目：[JPEG解码](http://118.190.20.162/view.page?gpid=T158)**

🟠 这种题目难度不是很大，但题目很长，需要把要求一步步分解，耐心计算即可。

🟡 蛇形矩阵填充，由于本题矩阵大小固定，可以 **用矩阵 idx\[8][8] 来存储对应位置填充的元素下标** ，就可以很方便的完成存储。

🔵 在循环里面 i++ 换成 ++i 更快一点。

```c++
#include<bits/stdc++.h>
using namespace std;

int q[8][8],m[8][8],sc[100]={0};
double mm[8][8],pi=acos(-1);
int n,t;

// 量化：m和q相乘 
void lh(){
	for(int i=0;i<8;++i)
		for(int j=0;j<8;++j)
			m[i][j] *= q[i][j]; 
}

// 剩余解码工作
void jm(){
	for(int i=0;i<8;++i){
		for(int j=0;j<8;++j){
			double a=1,ans=0;
			for(int u=0;u<8;++u){
				for(int v=0;v<8;++v){
					if(u==0 && v==0)	a=0.5;
					else if(u*v==0)	a=pow(0.5,0.5);
					else a=1;
					ans += a*m[u][v]*cos((i+0.5)*pi*u/8)*cos((j+0.5)*pi*v/8);
				}
			}
			mm[i][j] = ans/4;
		}
	}

	for(int i=0;i<8;++i){
		for(int j=0;j<8;++j){
			m[i][j] = (int)(mm[i][j]+128.5);
			if(m[i][j]>255)	m[i][j]=255;
			else if(m[i][j]<0)	m[i][j]=0;
		}
	}
}

int main(){
	// 输入
	for(int i=0;i<8;++i)
		for(int j=0;j<8;++j)
			scanf("%d",&q[i][j]);
	scanf("%d",&n);scanf("%d",&t);
	for(int i=0;i<n;++i)
		scanf("%d",&sc[i]);
	
	// 计算m
	int idx[8][8] = { {0,  1,  5,  6,  14, 15, 27, 28},
                     {2,  4,  7,  13, 16, 26, 29, 42},
                     {3,  8,  12, 17, 25, 30, 41, 43},
                     {9,  11, 18, 24, 31, 40, 44, 53},
                     {10, 19, 23, 32, 39, 45, 52, 54},
                     {20, 22, 33, 38, 46, 51, 55, 60},
                     {21, 34, 37, 47, 50, 56, 59, 61},
                     {35, 36, 48, 49, 57, 58, 62, 63} };
    for(int i=0;i<8;++i){
    	for(int j=0;j<8;++j)
    		m[i][j] = sc[idx[i][j]];
	}
	
	if(t>0)	lh();	// t=1或t=2：量化 
	if(t>1)	jm(); 	// t=2：完成剩余解码工作 

	// 输出答案 
	for(int i=0;i<8;++i){
		for(int j=0;j<8;++j)
			cout<<m[i][j]<<" ";
		cout<<endl;
	}
	return 0;
} 
```


