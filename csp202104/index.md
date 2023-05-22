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


