# 【CSP】202209题解


## 2

错误的：

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入书本数量 n，包邮条件 x 
	int n,x;
	scanf("%d",&n);
	scanf("%d",&x);
	
	// 输入书本价格，计算总和 sum 
	int a[n];
	int sum=0;
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
		sum+=a[i];
	} 
	
	// 动态规划，找前 i 本书的价格和最大，不超过 sum-x 
	int d[n][sum-x+1]={0};
	for(int j=0;j<sum-x+1;j++){
		d[0][j]=a[0]>j ? 0 : a[0];
	}
	
	for(int i=1;i<n;i++){
		for(int j=0;j<sum-x+1;j++){
			if(d[i-1][j-a[i]]+a[i] <=j){
				d[i][j]=max(d[i-1][j-a[i]]+a[i],d[i-1][j]);
			}
			else d[i][j]=d[i-1][j];
			
		}
	}
	

	printf("\n%d",sum-d[n-1][sum-x]);
	
} 
```

正确的：

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入书本数量 n，包邮条件 x 
	int n,x;
	scanf("%d",&n);
	scanf("%d",&x);
	
	// 输入书本价格，计算总和 sum 
	int a[n];
	int sum=0;
	for(int i=0;i<n;i++){
		scanf("%d",&a[i]);
		sum+=a[i];
	} 
	
	// 动态规划，找前 i 本书的价格和最大，不超过 sum-x 
	int d[n][sum-x+1]={0};
	for(int j=0;j<sum-x+1;j++){
		d[0][j]=a[0]>j ? 0 : a[0];
	}
	
	for(int i=1;i<n;i++){
		for(int j=0;j<sum-x+1;j++){
			if(a[i] <=j){
				d[i][j]=max(d[i-1][j-a[i]]+a[i],d[i-1][j]);
			}
			else d[i][j]=d[i-1][j];
			
		}
	}
	

	printf("%d",sum-d[n-1][sum-x]);
	
} 
```

## 3

错误的：

```c++
#include <iostream>
#include <cmath>

using namespace std;

struct mdata{
	int d;
	int u;
	int r;
}; 

struct ddata{
	int r;
	int m;
	int p[405];
	mdata my[305];
};

int main()
{

	// 输入变量
	int n;
	scanf("%d",&n);
	ddata day[n];
	for(int i=0;i<n;i++){
		scanf("%d",&day[i].r);	// 风险地区个数 
		scanf("%d",&day[i].m);	// 漫游数据个数 
		for(int j=1;j<=day[i].r;j++){
			scanf("%d",&day[i].p[j]);	// 当天的风险地区 
		}
		
		for(int j=0;j<day[i].m;j++){	// 漫游数据 
			scanf("%d",&day[i].my[j].d);
			scanf("%d",&day[i].my[j].u);
			scanf("%d",&day[i].my[j].r);
		}
	}
	
	int user[1000]={0};
	int cnt=0;
	for(int i=0;i<day[0].m;i++){
		
		for(int j=1;j<=day[0].r;j++){
			if(day[0].p[j]==day[0].my[i].r){
				user[day[0].my[i].u]=1;
				break;
			}
		}
	}
	
	for(int i=0;i<1000;i++){
		if(user[i]==1){
			printf("%d", user[i]);
		}
	}
	
	
} 
```


