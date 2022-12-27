# C++基础语法(三)


## 11 字符串

#### C风格字符串

字符串实际上是使用 **null** 字符 **\0** 终止的一维字符数组。

```c++
char site[7] = {'R', 'U', 'N', 'O', 'O', 'B', '\0'};
char site[] = "RUNOOB";
```

字符串相关函数：

- **strcpy(s1, s2);** 复制字符串 s2 到字符串 s1。
- **strcat(s1, s2);** 连接字符串 s2 到字符串 s1 的末尾。连接字符串也可以用 **+** 号。
- **strlen(s1);** 返回字符串 s1 的长度。
- **strcmp(s1, s2);** 如果 s1 和 s2 是相同的，则返回 0；如果 s1<s2 则返回值小于 0；如果 s1>s2 则返回值大于 0。
- **strchr(s1, ch);** 返回一个指针，指向字符串 s1 中字符 ch 的第一次出现的位置。
- **strstr(s1, s2);** 返回一个指针，指向字符串 s1 中字符串 s2 的第一次出现的位置。

#### String类

```c++
string str1 = "runoob";
string str2 = "google";
string str3;
int  len ;
 
// 连接 str1 和 str2
str3 = str1 + str2;
// 连接后，str3 的总长度为12！
len = str3.size();
```

## 12 指针


