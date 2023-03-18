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



## 3 

```c++
#include <bits/stdc++.h>
using namespace std;

long long w,s,f;
string x;
vector<long long>v;

int main(){
   cin>>w>>s>>x;							// 输入所有：w 每行能容纳的码字数，s 校验级别，x 字符串 

   long long tp=0;							// 记录上一个字符的类型：0 大写字母，1 小写字母，2 数字 

   for(long long i=0;i<x.length();i++){
      if(x[i]>='A'&&x[i]<='Z'){				// 这个是大写字母 
            if(tp==1){						// 小→大：28 28 
            v.push_back(28);
            v.push_back(28);
         }
         else if(tp==2){					// 数→大：28 
            v.push_back(28);                     
         }
         tp=0;								// 更新上一个字符类型为 0 
         v.push_back(x[i]-'A');				// 加入字符 
      }
      else if(x[i]>='a'&&x[i]<='z'){		// 这个是小写字母 
         if(tp==0||tp==2)v.push_back(27);	// 大→小、数→小：27 
         v.push_back(x[i]-'a');				// 加入字符 
         tp=1;								// 更新上一个字符类型为 1
      }
      else {								// 这个是数字 
         if(tp!=2)v.push_back(28);			// 大→数、小→数：28 
         tp=2								// 更新上一个字符类型为 2
         v.push_back(x[i]-'0');				// 加入字符
      }
   }

   if(v.size()%2){							// 如果数字有奇数个，就补齐 
      v.push_back(29);
   }
   vector<long long>v2;						// 存储码字 
   for(long long i=0;i<v.size();i+=2){		// 计算码字 
      v2.push_back(v[i]*30+v[i+1]);
   }
   
   long long len=v2.size()+1;				// 数据区码字个数=1（码字长度）+数据码字个数 
   if(s==-1){
      while(len%w){							// 用 900 补齐 
         v2.push_back(900);
         ++len;
      }
      cout<<len<<endl;						// 第一个是码字的数量 
      for(auto x:v2)cout<<x<<endl;
      return 0;
   }
   while((len+(1<<(s+1)))%w){				// 用 1<<(s+1) 计算校验码字的数目 
      ++len;
      v2.push_back(900);
   } 
 
   vector<long long>v3;						// v3 存储 dn 的系数，按高位到低位 
   v3.push_back(len);
   for(auto X:v2){
      v3.push_back(X);
   }
    for(auto x:v3)printf("%d\n",x);			// 输出码字长度+数据码字个数 

   vector<long long>v4;						// 存储 g(x) 的系数，按高位到低位，最高 k 次 
   v4.push_back(1);
   v4.push_back(-3);
   
   long long mul=-3;

   for(long long i=2;i<=(1<<(s+1));i++){				// 计算  g(x) 的系数
      v4.push_back(0);
      mul=mul*3%929; 									// x 的 i 次方 
      for(long long j=v4.size()-1;j>=1;j--)
	  	v4[j]+=v4[j-1]*mul%929,v4[j]%=929;
   }  
      long long w4=1<<(s+1);							// k					
      for(long long i=1;i<=w4;i++) v3.push_back(0);		// 把 v3 变成 x 的 k+n-1 次方的系数 
      for(long long i=0;i<v3.size();i++){				// i：相除时上的 x 的最高位 
          long long num=v3[i]; 							// x 的 i 次方的系数，除数 × 被除数最高位系数 
          for(long long j=i+1;j<=i+w4;j++)				// 相减时，最高位相同不用减，所以 j 从 i+1 开始，而 g(x) 除了最高位，最多还有 k 项 
		  	v3[j]-=num*v4[j-i]%929,v3[j]%=929;			// 被除数最高位需要对齐的系数也在变化 
          if(i==v3.size()-1-w4){ f=i+1; break; }    	// 
      }
      while(f<v3.size()){ 
      	long long ans1=(-v3[f])%929;  
      	if(ans1<0)ans1+=929; 
      	cout<<ans1<<endl;
      	++f;
      } 
      return 0;
}
```





```c++
#include <bits/stdc++.h>
using namespace std;

int main(){
	
	// 1. 输入数据 
	int w,s;									// 每行能容纳的码字数、校验级别
	string str;									// 输入的非空字符串 
	vector<int> v1;								//  最初的数字序列 	
    cin>>w>>s>>str;		
	int k=(s==-1 ? 0 : (1<<(s+1)));			
    
    
    // 2. 产生数字序列 
    int flag=-1;								// 表示一个数属于哪一类：0 大写，1 小写，2 数字 
    for(int i=0;i<str.length();i++){	
    	if(str[i]>='A' && str[i]<='Z'){			// 大写 
    		if(flag==1){						// 前一个是小写 
    			v1.push_back(28);
				v1.push_back(28); 
			}
			else if(flag==2)					// 前一个是数字
				v1.push_back(28); 
			v1.push_back(str[i]-'A');
			flag=0; 
		}
		else if(str[i]>='a' && str[i]<='z'){	// 小写 
			if(flag==0 || flag==2)				// 前一个是大写或数字 
				v1.push_back(27);	
			v1.push_back(str[i]-'a');
			flag=1;	
		}
		else if(str[i]>='0' && str[i]<='9'){	// 数字
			if(flag==0 || flag==1)				// 前一个是大写或数字
				v1.push_back(28);
			v1.push_back(str[i]-'0'); 
			flag=2;
		}
		 
	}
	
	
	// 3. 计算有效数据码字
	if(v1.size()%2)								// 只有奇数个字 
		v1.push_back(29);  
	vector<long long> v2;						// 存储有效数据的码字 
	v2.push_back(0);							// 第一个放数据码字的总个数，先占个坑位 
	for(int i=0;i<v1.size();i+=2){				// 加入有效数据 
		v2.push_back(30*v1[i]+v1[i+1]);
	}
	while((v2.size()+k)%w)						// 填充数据 
		v2.push_back(900); 
	v2[0]=v2.size();							// 更新数据码字的总个数 
	
	
	// 4. 输出 数据码字 v2，s=-1 时，直接退出 
	for(int i=0;i<v2.size();i++)
		cout<<v2[i]<<endl;
	if(s==-1)
		return 0;

	
	// 5. 计算 g(x) 中每项的系数
	vector<long long> v3;						// v3：存储 x 的系数，由高次到低次 
	v3.push_back(1);
	v3.push_back(-3); 
	long long t=-3;
	for(int i=2;i<=k;i++){
		t=(t*3)%929;
		v3.push_back(0);
		for(int j=v3.size()-1;j>=1;j--)
			v3[j]=(v3[j]+t*v3[j-1])%929;
	}
	
	
	// 6. 计算 d(x) 中每项的系数，存储在 v2 中 
	for(int i=0;i<k;i++)
		v2.push_back(0);
	
	
	// 7. 计算余数 r(x)
	int a;
	for(int i=0;i<v2.size();i++){
		for(int j=i+1;j<=i+k;j++)
			v2[j]=(v2[j]-v2[i]*v3[j-i])%929;	
		if(i==v2.size()-1-k){
			a=i+1;
			break;
		}		
	}
	
	
	// 8. 输出最终结果 
	while(a<v2.size()){
		if((-v2[a])%929<0)	
			cout<<(-v2[a])%929+929<<endl;
		else cout<<(-v2[a])%929<<endl;
		a++;
	}
	
	return 0;
}
```


