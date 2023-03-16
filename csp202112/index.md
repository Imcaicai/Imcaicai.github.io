# 【CSP】202112题解


## 1

```c++
// 需要用 CPP11 或 CPP14 
// #include<bits/stdc++.h>
#include <iostream>
using namespace std;
 
int main(){
	
	// 输入 n，m 
	int n,m;
	scanf("%d %d",&n,&m);
	
	// 输入数列
	int a[n+1]={0};
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
	} 
	
	// 计算
	int sum=0;
	int t=0;
	for(int i=1;i<m;i++){
		if(i>=a[n]){
			sum=sum+(m-i)*n;
			break;
		}
		
		for(int j=t+1;j<=n;j++){
			if(a[j]>i){
				t=j-1;
				sum=sum+t;
				break;
			}
		}
	}
	
	printf("%d", sum);
	
	
}
```

## 2

错误的：

```c++

```


