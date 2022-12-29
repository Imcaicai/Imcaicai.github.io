# C++面向对象(一)


# 1 类&对象

## 1.1 成员函数

成员函数可以定义在类定义内部：

```c++
class Box
{
   public:
      double length;      // 长度
      double breadth;     // 宽度
      double height;      // 高度
   
      double getVolume(void)
      {
         return length * breadth * height;
      }
};
```

也可以在类的外部使用**范围解析运算符 ::** 定义该函数。

```c++
class Box
{
   public:
      double length;         // 长度
      double breadth;        // 宽度
      double height;         // 高度
      double getVolume(void);// 返回体积
};

double Box::getVolume(void)
{
    return length * breadth * height;
}
```

在 :: 运算符之前必须使用类名。

## 1.2 类访问修饰符

关键字 **public、private、protected** 称为访问修饰符。

一个类可以有多个 public、protected 或 private 标记区域。每个标记区域在下一个标记区域开始之前或者在遇到类主体结束右括号之前都是有效的。成员和类的默认访问修饰符是 private。

```c++
class Base {
   public:
  // 公有成员
 
   protected:
  // 受保护成员
 
   private:
  // 私有成员
};
```

### public(公有成员)

**公有**成员在程序中类的外部是可访问的。您可以不使用任何成员函数来设置和获取公有变量的值

### private(私有成员)

**私有**成员变量或函数在类的外部是不可访问的，甚至是不可查看的。只有类和友元函数可以访问私有成员。默认情况下，类的所有成员都是私有的。

一般会在私有区域定义数据，在公有区域定义相关的函数，以便在类的外部也可以调用这些函数。

```c++
#include <iostream>
using namespace std;
 
class Box
{
   public:
      void setWidth( double wid );
      double getWidth( void );
   private:
      double width;
};
 
// 成员函数定义
double Box::getWidth(void)
{
    return width ;
}
 
void Box::setWidth( double wid )
{
    width = wid;
}
 
// 程序的主函数
int main( )
{
   Box box;
   // 不使用成员函数设置宽度
   // box.width = 10.0; // Error: 因为 width 是私有的
   box.setWidth(10.0);  // 使用成员函数设置宽度
   
   return 0;
}
```

### protected(受保护成员)

**protected（受保护）**成员变量或函数与私有成员十分相似，但有一点不同，protected（受保护）成员在派生类（即子类）中是可访问的。

### 继承中的特点

有public, protected, private三种继承方式，它们相应地改变了基类成员的访问属性。

- **public 继承：**基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：public, protected, private
- **protected 继承：**基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：protected, protected, private
- **private 继承：**基类 public 成员，protected 成员，private 成员的访问属性在派生类中分别变成：private, private, private

但无论哪种继承方式，上面两点都没有改变：

- private 成员只能被本类成员（类内）和友元访问，不能被派生类访问；
- protected 成员可以被派生类访问。

## 1.3 类构造函数&析构函数

### 构造函数

类的**构造函数**是类的一种特殊的成员函数，它会在每次创建类的新对象时执行。

构造函数的名称与类的名称是完全相同的，并且不会返回任何类型，也不会返回 void。构造函数可用于为某些成员变量设置初始值。

```c++
class Line
{
   public:
      Line();  // 这是构造函数
   private:
      double length;
};
 
// 成员函数定义，包括构造函数
Line::Line(void)
{
    cout << "Object is being created" << endl;
}

// 成员函数定义，包括构造函数
Line::Line( double len)
{
    cout << "Object is being created, length = " << len << endl;
    length = len;
}
```

### 使用初始化列表来初始化字段

```c++
// 相当于上面带参的构造函数
Line::Line( double len): length(len)
{
    cout << "Object is being created, length = " << len << endl;
}
```

假设有一个类 C，具有多个字段 X、Y、Z 等需要进行初始化，同理地，您可以使用上面的语法，只需要在不同的字段使用逗号进行分隔。

### 析构函数

类的**析构函数**是类的一种特殊的成员函数，它会在每次删除所创建的对象时执行。

析构函数的名称与类的名称是完全相同的，只是在前面加了个波浪号（~）作为前缀，它不会返回任何值，也不能带有任何参数。析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

```c++
Line::~Line(void)
{
    cout << "Object is being deleted" << endl;
}
```

## 1.4 拷贝构造函数

它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。拷贝构造函数通常用于：

- 通过使用另一个同类型的对象来初始化新创建的对象。
- 复制对象把它作为参数传递给函数。
- 复制对象，并从函数返回这个对象。

如果在类中没有定义拷贝构造函数，编译器会自行定义一个。如果类带有指针变量，并有动态内存分配，则它必须有一个拷贝构造函数。拷贝构造函数的最常见形式如下：

```c++
classname (const classname &obj) {   
	// 构造函数的主体 
}
```

在这里，**obj** 是一个对象引用，该对象是用于初始化另一个对象的。

## 1.5 友元函数

类的友元函数是定义在类外部，但有权访问类的所有私有（private）成员和保护（protected）成员。

友元可以是一个函数，该函数被称为友元函数；友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元。

如果要声明函数为一个类的友元，需要在类定义中该函数原型前使用关键字 **friend**。

```c++
class Box
{
public:
   friend void printWidth( Box box );
};
```

声明类 ClassTwo 的所有成员函数作为类 ClassOne 的友元，需要在类 ClassOne 的定义中放置如下声明：

```c++
friend class ClassTwo;
```

## 1.6 this指针

**this** 指针是所有成员函数的隐含参数。因此，在成员函数内部，它可以用来指向调用对象。

友元函数没有 **this** 指针，因为友元不是类的成员。只有成员函数才有 **this** 指针。

## 1.7 静态成员

当我们声明类的成员为静态时，这意味着无论创建多少个类的对象，静态成员都只有一个副本。

我们不能把静态成员的初始化放置在类的定义中，但是可以在类的外部通过使用范围解析运算符 **::** 来重新声明静态变量从而对它进行初始化。

```c++
class Box
{
   public:
      static int objectCount;  
    ......
};
 
// 初始化类 Box 的静态成员
int Box::objectCount = 0;
 
int main(void)
{
	......
   // 输出对象的总数
   cout << "Total objects: " << Box::objectCount << endl;
    ......
}
```

### 静态成员函数

静态成员函数即使在类对象不存在的情况下也能被调用，**静态函数**只要使用类名加范围解析运算符 **::** 就可以访问。

> **静态成员函数与普通成员函数的区别：**
>
> - 静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）。
> - 普通成员函数有 this 指针，可以访问类中的任意成员；而静态成员函数没有 this 指针。

