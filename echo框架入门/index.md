# Echo框架入门


## 1 路由与控制器

### 1 路由规则

- 一条路由规则由：`http请求方法` , `url路径` , `控制器函数 ` 组成

**1.http请求方法**

- GET
- POST
- PUT
- DELETE

**2.url路径**

- 静态url路径
- 带路径参数的url路径
- 带星号（*）模糊匹配参数的url路径

```go
// 例子1， 静态Url路径, 即不带任何参数的url路径
/users/center
/user/101
/food/100

// 例子2，带路径参数的url路径，url路径上面带有参数,参数由冒号（:）跟着一个字符串定义。
// 路径参数值可以是数值，也可以是字符串

//定义参数:id， 可以匹配/user/1, /user/899 /user/xiaoli 这类Url路径
/user/:id

//定义参数:id， 可以匹配/food/2, /food/100 /food/apple 这类Url路径
/food/:id

//定义参数:type和:page， 可以匹配/foods/2/1, /food/100/25 /food/apple/30 这类Url路径
/foods/:type/:page

// 例子3. 带星号（*）模糊匹配参数的url路径
// 星号代表匹配任意路径的意思

//匹配：/foods/1， /foods/200, /foods/1/20, /foods/apple/1 
//以/foods/ 开头的所有路径都匹配
/foods/*
```

**3.url路径匹配顺序**

如果出现，一个http请求路径匹配多个定义的url路径，echo框架按下面顺序匹配，先匹配到那个就用那个定义。

- 匹配静态url路径
- 匹配带路径参数的url路径
- 匹配带星号（*）模糊匹配参数的url路径

**4.控制器函数**

控制器函数接受一个上下文参数，并返回一个错误。可以通过上下文参数，获取http请求参数，响应http请求。

```go
func HandlerFunc(c echo.Context) error{}
```

### 2 示例

> 实际项目开发中不要把路由定义和控制器函数都写在一个go文件，不方便维护。

```go 
//实例化echo对象。
e := echo.New()
	
//定义post请求, url路径为：/users, 绑定saveUser控制器函数
e.POST("/users", saveUser)

//定义get请求，url路径为：/users/:id  （:id是参数，例如: /users/10, 会匹配这个url模式），绑定getUser控制器函数
e.GET("/users/:id", getUser)

//定义put请求
e.PUT("/users/:id", updateUser)

//定义delete请求
e.DELETE("/users/:id", deleteUser)


//控制器函数实现
func saveUser(c echo.Context) error {
    ...忽略实现...
}

func getUser(c echo.Context) error {
    ...忽略实现...
}

func updateUser(c echo.Context) error {
    ...忽略实现...
}

func deleteUser(c echo.Context) error {
    ...忽略实现...
}
```

## 2 处理请求参数

### 1 绑定数据

通过将请求参数绑定到一个struct对象的方式获取数据。这种方式获取请求参数支持json、xml、k/v键值对等多种方式。

```go
// User 结构体定义
type User struct {
  Name  string `json:"name" form:"name" query:"name"`
  Email string `json:"email" form:"email" query:"email"`
}
```

控制器代码：

```go
// Handler
func(c echo.Context) (err error) {
  u := new(User)
  //调用echo.Context的Bind函数将请求参数和User对象进行绑定。
  if err = c.Bind(u); err != nil {
    return
  }
  
  //请求参数绑定成功后 u 对象就保存了请求参数。
  //这里直接将请求参数以json格式显示
  //注意：User结构体,字段标签定义中，json定义的字段名，就是User对象转换成json格式对应的字段名。
  return c.JSON(http.StatusOK, u)
}
```

### 2 获取post请求数据

通过echo.Context对象的 `FormValue` 函数可以直接获取post请求参数。

> 通过FormValue函数获取参数的值，数据类型都是String类型， 如果需要其他类型的数据，需要自己转换数据格式。

```go
// Handler
func(c echo.Context) error {
    //获取name参数
	name := c.FormValue("name")
	
	//直接输出name参数
	return c.String(http.StatusOK, name)
}
```

### 3 获取get请求数据

通过echo.Context对象的 `QueryParam` 函数可以直接获取get请求参数。

```go
// Handler
func(c echo.Context) error {
    //获取name参数, 通过QueryParam获取的参数值也是String类型。
	name := c.QueryParam("name")
	//直接输出name参数
	return c.String(http.StatusOK, name)
})
```

### 4 获取path路径参数

通过echo.Context对象的 `Param` 获取，url路径参数。

```go
//例子: url路由规则为/users/:name  , :name为参数。
e.GET("/users/:name", func(c echo.Context) error {
    //获取路径参数:name的值
	name := c.Param("name")
	//如果请求url为: /users/tizi365 则name的值为tizi365
	//Param获取的值也是String类型
	return c.String(http.StatusOK, name)
})
```

## 3 处理请求结果

**1 以字符串方式响应**

```go
String(code int, s string) error
```

**2 以json格式响应**

```go
JSON(code int, i interface{}) error
```

**3 以xml格式响应**

```go
XML(code int, i interface{}) error
```

**4 以文件格式响应**

**5 设置http响应头**

```go
func(c echo.Context) error {
  //设置http响应 header
  c.Response().Header().Add("tizi", "tizi365")
  
  return c.String(200, "欢迎访问tizi360.com!")
}
```

## 4 访问静态资源文件

echo通过static中间件支持静态资源文件的访问。我们可以通过echo.Static函数初始化static中间件。

```go
Static(prefix, root string) *Route
```

```go
//初始化echo实例
e := echo.New()

//设置Static中间件
//如果我们访问 /res/tizi.jpg这个url路径，实际上就是访问static/tizi.jpg这个路径的内容
e.Static("/res", "static")
```

我们也可以通过**Echo.File**函数为一个url地址绑定一个静态资源文件。

```go
//初始化echo实例
e := echo.New()

//访问 / 就是访问public/index.html文件， index.html相当于站点默认首页
e.File("/", "public/index.html")

//访问/favicon.ico 就是访问images/favicon.ico文件， 相当于为站点设置了图标
e.File("/favicon.ico", "images/favicon.ico")
```


