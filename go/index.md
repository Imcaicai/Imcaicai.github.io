# Go基本语法


## 1 结构

- `go run helloworld.go`：执行Go代码
- `go build helloworld.go`：编译生成二进制文件
- `./helloworld`：运行
- `import` 声明必须跟在文件的 `package` 声明之后
- Go 语言不需要在语句或者声明的末尾添加分号，除非一行上有多条语句
  - 函数的左括号 `{` 必须和 `func` 函数声明在同一行上，且位于末尾，不能独占一行
  - 在表达式 `x+y` 中，可在 `+` 后换行，不能在 `+` 前换行

## 2 基础语法

```go
//格式化字符串
var stockcode = 123
var enddate = "2020-12-31"
var url = "Code=%d&endDate=%s"
var target_url = fmt.Sprintf(url, stockcode, enddate)
fmt.Println(target_url)
```

## 3 语言类型

- 布尔型
- 数字型
  - 整形：`int uint`
  - 浮点型：`float complex`

- 字符串
- 派生类型

## 4 变量

- 变量声明

  - `var identifier type`（指定变量类型，如果没有初始化，则变量默认为零值
  - `var v_name = value`（根据值自行判断变量类型
  - `v_name := value`（只能在函数体中出现

- ```go
  // 这种因式分解关键字的写法一般用于声明全局变量
  var (
      vname1 v_type1
      vname2 v_type2
  )
  ```

- 局部变量不允许声明但不使用，全局变量可以

- `a, b = b, a` （简单交换2个变量

- `_`：空白标识符，也用于被抛弃值

## 5 常量

- `const identifier [type] = value`

- ```go
  //用作枚举
  const (
      Unknown = 0
      Female = 1
      Male = 2
  )
  ```

- 常量可以用`len(), cap(), unsafe.Sizeof()`函数计算表达式的值（必须是内置函数

- `iota` 在`const`关键字出现时将被重置为0，const中每新增一行常量声明将使 iota 计数一次

## 6 条件语句

- switch

  - 匹配项后面也不需要再加 break

  - `fallthrough` 执行后面的case

  - case后面是类型不被局限于常量或整数，可以加多个，必须类型相同

  - ```go
    func main() {
    	var grade string = "B"
    	var marks int = 90
    
    	switch marks {
    	case 90:
    		grade = "A"
    	case 80,70:
    		grade = "B"
    	default:
    		grade = "D"
    	}
    
    	switch {
    	case grade == "A":
    		fmt.Println("youxiu")
    	case grade == "B", grade == "C":
    		fmt.Println("lianghao")
    	default:
    		fmt.Println("cha")
    	}
    }
    ```

- `type switch`  判断某个 interface 变量中实际存储的变量类型

- `select` 通信的 switch 语句

## 7 循环语句

- for循环

  - `for init; condition; post { }`

  - `for condition { }`

  - `for { }`

  - ```go
    //range格式可以对 slice、map、数组、字符串等进行迭代循环
    for key, value := range oldMap {
        newMap[key] = value
    }
    
    for key := range oldMap
    for _, value := range oldMap
    ```

- 在多重循环中，可以用标号 label 标出想 `break` 的循环

- 在多重循环中，可以用标号 label 标出想 `continue` 的循环

- `goto` 语句可以无条件地转移到过程中指定的行

## 8 函数

- ```go
  func function_name( [parameter list] ) [return_types] {
     函数体
  }
  ```

- 函数可作为实参

- 匿名函数，可作为闭包

- ```go
  //方法
  func (variable_name variable_data_type) function_name() [return_type]{
     /* 函数体*/
  }
  ```

## 9 变量作用域

- 局部变量：作用域只在函数体内
- 全局变量：整个包甚至外部包（被导出后）使用
- 全局变量与局部变量名称可以相同，但是函数内的局部变量会被优先考虑

## 10 数组

- `var variable_name [SIZE] variable_type`

- 可以使用 `...` 代替数组的长度

- ```go
  //  将索引为 1 和 3 的元素初始化
  balance := [...]float32{1:2.0,3:7.0}
  ```

- 多维数组

  - 使用 append() 函数向空的二维数组添加两行一维数组

  - ```go
    //多维数组
    var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type
    ```

  - 可以创建各个维度元素数量不一致的多维数组

- ```go
  //向函数传递数组
  void myFunction(param [10]int)
  {
  	...
  }
  ```

## 11 指针

- `var var_name *var-type`

- 指针数组

  - ```go
    var ptr [MAX]*int;
    ```

- 指向指针的指针

  - ```go
    var ptr **int;
    ```

## 12 结构体

- ```go
  //定义结构体
  type struct_variable_type struct {
     member definition
     ...
     member definition
  }
  
  //声明变量
  variable_name := structure_variable_type {value1, value2...valuen}
  variable_name := structure_variable_type { key1: value1, key2: value2..., keyn: valuen}
  ```

- 访问结构体：`结构体.成员名`

- 结构体作为函数参数

- 结构体指针

  - ```go
    //声明
    var struct_pointer *Books
    ```

  - 结构体指针用 `.` 访问结构体成员

## 13 切片(Slice)

- ```go
  var identifier []type	//定义切片
  var slice1 []type = make([]type, len)	//创建切片
  make([]T, length, capacity)	//capacity指定容量，为可选参数
  ```

- ```go
  s :=[] int {1,2,3 }	//直接初始化切片 
  s := arr[:] 	//初始化切片 s，是数组 arr 的引用
  s := arr[startIndex:endIndex]	//将 arr 中从下标 startIndex 到 endIndex-1 下的元素创建为一个新的切片
  
  ```

- `len()` 方法获取长度

- `cap()` 可以测量切片最长可以达到多少

- 空切片(nil)：切片未初始化，默认为nil，长度为0

- `copy()` 方法拷贝切片

- `append()` 方法向切片追加新元素 

## 14 范围(range)

- 关键字用于 for 循环中迭代`数组(array)、切片(slice)、通道(channel)或集合(map)`的元素

- 在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对

- ```go 
  //读取key,value
  for key, value := range oldMap {
      newMap[key] = value
  }
  //只读取key
  for key := range oldMap
  //只读取value
  for _, value := range oldMap
  ```

- range也可以用来枚举 Unicode 字符串

## 15 集合(Map)

- 无序的键值对的集合

- ```go 
  /* 声明变量，默认 map 是 nil */
  var map_variable map[key_data_type]value_data_type
  
  /* 使用 make 函数 */
  map_variable := make(map[key_data_type]value_data_type)
  ```

- `delete()` 函数用于删除集合的元素, 参数为 map 和其对应的 key

## 16 接口

```go 
/* 定义接口 */
type interface_name interface {
  method_name1 [return_type]
  method_name2 [return_type]
  ...
  method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
  /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
  /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
  /* 方法实现*/
}
```

## 17 错误处理

```go 
type error interface {
    Error() string
}

func Sqrt(f float64) (float64, error) {
    if f < 0 {
        return 0, errors.New("math: square root of negative number")
    }
    // 实现
}
```

## 18 并发

- ```go
  //goroutine语法
  go 函数名( 参数列表 )
  ```

- 同一个程序中的所有 `goroutine` 共享同一个地址空间

- 通道（channel）

  - 是用来传递数据的一个数据结构

  - 通道可用于两个 `goroutine` 之间通过传递一个指定类型的值来同步运行和通讯

  - 操作符 `<-` 用于指定通道的方向，发送或接收。如果未指定方向，则为双向通道

  - ```go
    //声明通道
    ch := make(chan int)
    //设置发送缓冲区
    ch := make(chan int, 100)
    //遍历通道
    v, ok := <-ch
    //关闭通道
    cl
    ```

参考资料：

[Go语言教程](https://www.runoob.com/go/go-tutorial.html)

