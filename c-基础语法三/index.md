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

#### 指针声明

```c++
int    *ip;    /* 一个整型的指针 */
double *dp;    /* 一个 double 型的指针 */
float  *fp;    /* 一个浮点型的指针 */
char   *ch;    /* 一个字符型的指针 */
```

所有指针的值的实际数据类型，不管是整型、浮点型、字符型，还是其他的数据类型，都是一样的，都是一个代表内存地址的长的十六进制数。不同数据类型的指针之间唯一的不同是，指针所指向的变量或常量的数据类型不同。

#### Null 指针

在变量声明的时候，如果没有确切的地址可以赋值，为指针变量赋一个 NULL 值是一个良好的编程习惯。赋为 NULL 值的指针被称为**空**指针。

```c++
int  *ptr = NULL;	// ptr的值是0
```

#### 指针的算术运算

假设 **ptr** 是一个指向地址 1000 的整型指针，是一个 32 位的整数。

🟡 **递增一个指针**

```c++
ptr++;
```

在执行完上述的运算之后，**ptr** 将指向位置 1004，因为 ptr 每增加一次，它都将指向下一个整数位置，即当前位置往后移 4 个字节。这个运算会在不影响内存位置中实际值的情况下，移动指针到下一个内存位置。如果 **ptr** 指向一个地址为 1000 的字符，上面的运算会导致指针指向位置 1001，因为下一个字符位置是在 1001。

变量指针可以递增，而数组不能递增，因为数组是一个常量指针。

```c++
int  var[MAX] = {10, 100, 200};
for (int i = 0; i < MAX; i++)
{
   *var = i;    // 这是正确的语法
   var++;       // 这是不正确的，数组指针为常量
}
```

🔵 **指针的比较**

指针可以用关系运算符进行比较，如 ==、< 和 >。如果 p1 和 p2 指向两个相关的变量，比如同一个数组中的不同元素，则可对 p1 和 p2 进行大小比较。

#### 指向指针的指针

当我们定义一个指向指针的指针时，第一个指针包含了第二个指针的地址，第二个指针指向包含实际值的位置。

```c++
int  var = 300;
int  *ptr;
int  **pptr;

ptr = &var;	// 获取 var 的地址
pptr = &ptr;	// 使用运算符 & 获取 ptr 的地址

cout << "var 值为 :" << var << endl;	// 3000
cout << "*ptr 值为:" << *ptr << endl;	// 3000
cout << "**pptr 值为:" << **pptr << endl;	// 3000
```

#### 传递指针给函数

#### 从函数返回指针

## 13 引用

引用变量是一个别名，也就是说，它是某个已存在变量的另一个名字。一旦把引用初始化为某个变量，就可以使用该引用名称或变量名称来指向变量。

#### C++ 引用 vs 指针

- 不存在空引用。引用必须连接到一块合法的内存。
- 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
- 引用必须在创建时被初始化。指针可以在任何时间被初始化。

#### 创建引用

& 读作**引用**。

```c++

int    i;	// 声明简单的变量
// r 是一个初始化为 i 的整型引用
int&    r = i;	// 声明引用变量

i = 5;
cout << "Value of i : " << i << endl;	// 5
cout << "Value of i reference : " << r  << endl;	// 5
```

#### 把引用作为参数

#### 把引用作为返回值

```c++
double vals[] = {10.1, 12.6, 33.1, 24.1, 50.0};
 
double& setValues(int i) {  
   double& ref = vals[i];    
   return ref;   // 返回第 i 个元素的引用，ref 是一个引用变量，ref 引用 vals[i]
 
 
}
 
int main ()
{ 
   setValues(1) = 20.23; // 改变第 2 个元素
   cout << vals[1] << endl;	// 由12.6变为20.23
   return 0;
}
```

当返回一个引用时，要注意被引用的对象不能超出作用域。所以返回一个对局部变量的引用是不合法的，但是，可以返回一个对静态变量的引用。

```c++
int& func() {   
    int q;   
    //! return q; // 在编译时发生错误   
    static int x;   
    return x;     // 安全，x 在函数作用域外依然是有效的 
}
```

## 14 日期 & 时间

C++ 标准库没有提供所谓的日期类型。C++ 继承了 C 语言用于日期和时间操作的结构和函数。为了使用日期和时间相关的函数和结构，需要在 C++ 程序中引用 \<ctime> 头文件。

#### 相关数据类型

有四个与时间相关的类型：**clock_t、time_t、size_t** 和 **tm**。类型 clock_t、size_t 和 time_t 能够把系统时间和日期表示为某种整数。

结构类型 **tm** 把日期和时间以 C 结构的形式保存，tm 结构的定义如下：

```c++
struct tm {
  int tm_sec;   // 秒，正常范围从 0 到 59，但允许至 61
  int tm_min;   // 分，范围从 0 到 59
  int tm_hour;  // 小时，范围从 0 到 23
  int tm_mday;  // 一月中的第几天，范围从 1 到 31
  int tm_mon;   // 月，范围从 0 到 11
  int tm_year;  // 自 1900 年起的年数
  int tm_wday;  // 一周中的第几天，范围从 0 到 6，从星期日算起
  int tm_yday;  // 一年中的第几天，范围从 0 到 365，从 1 月 1 日算起
  int tm_isdst; // 夏令时
};
```

#### 关于日期和时间的重要函数

- **[ time_t time(time_t \*time);](https://www.runoob.com/cplusplus/c-function-time.html)** 该函数返回系统的当前日历时间，自 1970 年 1 月 1 日以来经过的秒数。如果系统没有时间，则返回 -1。

- [**char \*ctime(const time_t \*time);**](https://www.runoob.com/cplusplus/c-function-ctime.html) 该返回一个表示当地时间的字符串指针，字符串形式 *day month year hours:minutes:seconds year\n\0*。
- [**struct tm \*localtime(const time_t \*time);**](https://www.runoob.com/cplusplus/c-function-localtime.html) 该函数返回一个指向表示本地时间的 **tm** 结构的指针。
- [**clock_t clock(void);**](https://www.runoob.com/cplusplus/c-function-clock.html) 该函数返回程序执行起（一般为程序的开头），处理器时钟所使用的时间。如果时间不可用，则返回 -1。
- [**char \* asctime ( const struct tm \* time );**](https://www.runoob.com/cplusplus/c-function-asctime.html) 该函数返回一个指向字符串的指针，字符串包含了 time 所指向结构中存储的信息，返回形式为：day month date hours:minutes:seconds year\n\0。
- [**struct tm \*gmtime(const time_t \*time);**](https://www.runoob.com/cplusplus/c-function-gmtime.html) 该函数返回一个指向 time 的指针，time 为 tm 结构，用协调世界时（UTC）也被称为格林尼治标准时间（GMT）表示。
-   [**time_t mktime(struct tm \*time);**](https://www.runoob.com/cplusplus/c-function-mktime.html) 该函数返回日历时间，相当于 time 所指向结构中存储的时间。
- [**double difftime ( time_t time2, time_t time1 );**](https://www.runoob.com/cplusplus/c-function-difftime.html) 该函数返回 time1 和 time2 之间相差的秒数。
- [**size_t strftime();**](https://www.runoob.com/cplusplus/c-function-strftime.html) 该函数可用于格式化日期和时间为指定的格式。

#### 当前日期和时间

```c++
time_t now = time(0);	// 基于当前系统的当前日期/时间
char* dt = ctime(&now);	// 把 now 转换为字符串形式
cout << "本地日期和时间：" << dt << endl;	// Sat Jan  8 20:07:41 2011

// 把 now 转换为 tm 结构
tm *gmtm = gmtime(&now); 
dt = asctime(gmtm);   
cout << "UTC 日期和时间："<< dt << endl;	// Sat Jan  9 20:07:41 2011    
```

#### 使用结构 tm 格式化时间

```c++
// 基于当前系统的当前日期/时间
time_t now = time(0);
tm *ltm = localtime(&now);

// 输出 tm 结构的各个组成部分
cout << "年: "<< 1900 + ltm->tm_year << endl;
cout << "月: "<< 1 + ltm->tm_mon<< endl;
cout << "日: "<<  ltm->tm_mday << endl;
```

## 15 基本的输入输出

#### 标准输出流（cout）

**cout** 是与流插入运算符 << 结合使用的，<< 运算符被重载来输出内置类型（整型、浮点型、double 型、字符串和指针）的数据项。

#### 标准输入流（cin）

**cin** 是与流提取运算符 >> 结合使用的。

```c++
cin >> name  >> age;
cout << "您的名称是： " << name << endl;
cout << "您的年龄是： " << age << endl;
```

#### 标准错误流（cerr）

**cerr** 对象是非缓冲的，且每个流插入到 cerr 都会立即输出。

```c++
char str[] = "Unable to read....";
cerr << "Error message : " << str << endl;
// 输出结果为 Error message : Unable to read....
```

#### 标准日志流（clog）

```c++
char str[] = "Unable to read....";
clog << "Error message : " << str << endl;
// 输出结果为 Error message : Unable to read....
```

## 16 数据结构

#### 定义结构

```c++
struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} book;
```

#### 访问结构成员

使用**成员访问运算符（.）**。成员访问运算符是结构变量名称和我们要访问的结构成员之间的一个句号。

```c++
Books Book1;        // 定义结构体类型 Books 的变量 Book1

// Book1 详述
strcpy( Book1.title, "C++ 教程");
strcpy( Book1.author, "Runoob"); 
strcpy( Book1.subject, "编程语言");
Book1.book_id = 12345;
```

#### 结构作为函数参数

```c++
void printBook( struct Books book )
{
   cout << "书标题 : " << book.title <<endl;
   cout << "书作者 : " << book.author <<endl;
   cout << "书类目 : " << book.subject <<endl;
   cout << "书 ID : " << book.book_id <<endl;
}
```

#### 指向结构的指针

为了使用指向该结构的指针访问结构的成员，您必须使用 -> 运算符.

```c++
int main( )
{
   ......
   printBook( &Book1 );	// 通过传 Book1 的地址来输出 Book1 信息
   ......
 }

// 该函数以结构指针作为参数
void printBook( struct Books *book )
{
   cout << "书标题  : " << book->title <<endl;
   cout << "书作者 : " << book->author <<endl;
   cout << "书类目 : " << book->subject <<endl;
   cout << "书 ID : " << book->book_id <<endl;
}
```

#### typedef 关键字

下面是一种更简单的定义结构的方式，您可以为创建的类型取一个"别名"。例如：

```c++
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
}BO;
BO Book1, Book2;
```

您可以使用 **typedef** 关键字来定义非结构类型，如下所示：

```c++
typedef long int *pint32;
pint32 x;	// x是指向长整型 long int 的指针。
```


