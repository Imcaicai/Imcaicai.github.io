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
#include <iostream>
using namespace std;
int main()
{

	// 输入天数n 科目数m
	int n,m;
	scanf("%d",&n);
	scanf("%d",&m);
	// 输入依赖科目p[i]
	int p[m+1]={0},t[m+1]={0};
	for(int i=1;i<=m;i++){
		scanf("%d",&p[i]);
	} 
	
	// 输入科目所需天数t[i]
	for(int i=1;i<=m;i++){
		scanf("%d",&t[i]);
	} 
	
	// 计算最早开始天数
	int flag=0;
	int beg[m+1]={0},end[m+1]={0};
	for(int i=1;i<=m;i++){
		if(p[i]==0){
			beg[i]=1;
			end[i]=t[i];
		}
		else{
			beg[i]=end[p[i]]+1;
			end[i]=beg[i]+t[i]-1;
		}
		
		if(end[i]>n){
			flag=1;
		}
	}
	
	for(int i=1;i<=m;i++)
		printf("%d ",beg[i]);
	
	// 计算最晚开始天数
	if(flag==1) {
		return 0;
	}
	else{
		// 存储当前科目被哪个科目依赖 
		int last[m+1]={0}; 
		for(int i=m;i>0;i--){
			last[i]=n+1;	// 记录依赖当前科目的科目中，最早的【最晚开始时间】  
			for(int j=i+1;j<=m;j++){
				if(p[j]==i){
					last[i]=min(last[i],last[j]);
				}
			}
			
			last[i]=last[i]-t[i];
		}
		
		printf("\n");
		for(int i=1;i<=m;i++)
			printf("%d ",last[i]);
	}
} 
```

## 3 JPEG解码

🔗 **题目：[JPEG解码](http://118.190.20.162/view.page?gpid=T158)**

🟠 这种题目难度不是很大，但题目很长，需要把要求一步步分解，耐心计算即可。

🟡 蛇形矩阵填充，由于本题矩阵大小固定，可以 **用矩阵 idx\[8][8] 来存储对应位置填充的元素下标** ，就可以很方便的完成存储。

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入量化矩阵 Q
	int Q[8][8];
	for(int i=0;i<8;i++){
		for(int j=0;j<8;j++){
			scanf("%d",&Q[i][j]);
		}
	} 
	
	// 输入扫描个数 n，任务 T
	int n,T;
	scanf("%d",&n);
	scanf("%d",&T);
	
	// 输入一组扫描数据
	int d[64]={0};
	for(int i=0;i<n;i++){
		scanf("%d",&d[i]);
	} 
	
	// 将扫描数据放入填充矩阵 M 中 
	int M[8][8]={0};
	int idx[8][8] = { {0,  1,  5,  6,  14, 15, 27, 28},
                     {2,  4,  7,  13, 16, 26, 29, 42},
                     {3,  8,  12, 17, 25, 30, 41, 43},
                     {9,  11, 18, 24, 31, 40, 44, 53},
                     {10, 19, 23, 32, 39, 45, 52, 54},
                     {20, 22, 33, 38, 46, 51, 55, 60},
                     {21, 34, 37, 47, 50, 56, 59, 61},
                     {35, 36, 48, 49, 57, 58, 62, 63} };

	for(int i=0;i<8;i++){
		for(int j=0;j<8;j++){
			M[i][j]=d[idx[i][j]];
		}
	}
	
	// 输出 M 矩阵
	if(T==0){
		for(int i=0;i<8;i++){
			for(int j=0;j<8;j++){
				printf("%d ", M[i][j]);
			}
			printf("\n");
		} 
		
		return 0;
	}
	
	// 计算与量化矩阵 Q 相乘后的矩阵 M
	for(int i=0;i<8;i++){
		for(int j=0;j<8;j++){
			M[i][j]=M[i][j]*Q[i][j];
		}
	} 
	
	// 输出与量化矩阵 Q 相乘后的矩阵 M
	if(T==1){
		for(int i=0;i<8;i++){
			for(int j=0;j<8;j++){
				printf("%d ", M[i][j]);
			}
		
			printf("\n");
		} 
		
		return 0;
	}
	
	// 对 M 进行离散余弦逆变换 
	int MM[8][8];
	for(int i=0;i<8;i++){
		for(int j=0;j<8;j++){
			double s=0;
			for(int u=0;u<8;u++){
				for(int v=0;v<8;v++){
					double temp=cos(acos(-1)*u*(i+0.5)/8)*cos(acos(-1)*v*(j+0.5)/8)*M[u][v];
					if(u==0)
						temp *= pow(0.5,0.5);
					if(v==0)
						temp *= pow(0.5,0.5);
					s+=temp;
				}
			} 
			s=s/4;
			MM[i][j]=(int)(s+128.5);
			MM[i][j] = MM[i][j]>255 ? 255 : MM[i][j];
			MM[i][j] = MM[i][j]<0 ? 0 : MM[i][j];
		}
	} 
	
	// 输出与量化矩阵 Q 相乘后的矩阵 M
	if(T==2){
		for(int i=0;i<8;i++){
			for(int j=0;j<8;j++){
				printf("%d ", MM[i][j]);
			}
			printf("\n");
		} 
		
		return 0;
	}
	
} 
```


