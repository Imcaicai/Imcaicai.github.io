# 【STL】pair容器用法


## 1 简介

🟠 pair 容器将 2 个普通元素 first 和 second（C++ 基本数据类型、结构体、类自定的类型等）创建成一个新元素`<first, second>`。

🔵 使用需加上头文件：`#include <utility>`

## 2 创建map容器

1️⃣ 调用 pair 容器类的默认构造函数。

```c++
pair <string, int> pair1;
```

2️⃣ 在创建 pair 容器的同时，进行初始化。

```c++
pair <string, int> pair2("语文",90);
```

3️⃣ 利用先前已创建好的 pair 容器和拷贝构造函数，再创建一个新的 pair 容器。

```c++
pair <string, int> pair3(pair2);
```

## 3 常见用法

**3.1 手动为 pair 对象赋值**

```c++
pair1.first = "数学";
pair1.second = "100";
```

**3.2 比较2个pair对象**

先比较 pair.first 元素的大小，如果相等则继续比较 pair.second 元素的大小。对于进行比较的 2 个 pair 对象，其对应的键和值的类型比较相同

**3.3 swap() 函数**

能够互换 2 个 pair 对象的键值对，其操作成功的前提是这 2 个 pair 对象的键和值的类型要相同。

