# 【CSP】202104题解


## 1 灰度直方图

🔗 **题目：[灰度直方图](http://118.190.20.162/view.page?gpid=T128)**

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,m,l,a,h[300]={0};
	scanf("%d %d %d",&n,&m,&l);
	for(int i=0;i<n;i++){
		for(int j=0;j<m;j++){
			scanf("%d",&a);
			h[a]++;
		}
	}
	for(int i=0;i<l;i++)
		printf("%d ",h[i]);
	return 0;
}
```

## 2 邻域均值

🔗 **题目：[邻域均值](http://118.190.20.162/view.page?gpid=T127)**

🔴 求矩阵中一个框内的元素和：用二维压缩数组

🔵 判断时注意整数除法会向下取整，不能用 `if(sum/t<=num)` ，而应用 `if(sum<=t*num)` 。

```c++
#include<bits/stdc++.h>
using namespace std;

int main(){
	int n,l,r,t,cnt=0,x1,x2,y1,y2,sum,num;
	static int a[605][605]={0},s[605][605]={0};
	scanf("%d %d %d %d",&n,&l,&r,&t);	// 矩阵大小、元素范围、邻域参数、阈值 
	for(int i=1;i<=n;i++){
		for(int j=1;j<=n;j++){
			scanf("%d",&a[i][j]);
			s[i][j] = s[i][j-1]+s[i-1][j]-s[i-1][j-1]+a[i][j];
		}	
	}
	
	for(int i=1;i<=n;i++){
		for(int j=1;j<=n;j++){
			x1=max(1,i-r),x2=min(n,i+r);
			y1=max(1,j-r),y2=min(n,j+r);
			sum=s[x2][y2]-s[x1-1][y2]-s[x2][y1-1]+s[x1-1][y1-1];
			num=(x2-x1+1)*(y2-y1+1);
			if(sum<=t*num) cnt++;	// 注意这里，用乘法判断！！！
		}
	}
	cout<<cnt;
	return 0;
} 
```


