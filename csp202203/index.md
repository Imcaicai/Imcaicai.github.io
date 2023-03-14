# 【CSP】202203题解


## 1

错误的：

```c++
#include <iostream>
#include <cmath>
// #define _USE_MATH_DEFINES 

using namespace std;
int main()
{

	// 输入变量个数 n，语句个数 k
	int n,k;
	scanf("%d",&n);
	scanf("%d",&k);
	
	// 输入语句
	int x[n+1]={0},y[n+1]={0},a[n+1]={0};
	for(int i=1;i<=k;i++){
		scanf("%d",&x[i]);
		scanf("%d",&y[i]);
	} 
	
	for(int i=1;i<=k;i++){
		if(a[x[i]]==0){
			a[x[i]]=i;
		}
	}
	
	// 记录不符合的语句个数
	int cnt=0;
	for(int i=1;i<=k;i++){
		if(y[i]!=0 && i<=a[y[i]]){
			cnt++;
		}
	} 
	
	printf("%d",cnt);
	
	
} 
```

正确的：


