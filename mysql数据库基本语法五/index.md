# Mysql数据库基本语法(五)


# 9 数据库应用开发（JAVA篇）

## 9.1 JDBC体系结构和简单的查询

#### JDBC的体系结构

JDBC（Java DataBase Connectivity,java数据库连接）是一种用于执行SQL语句的Java API，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。JDBC提供了一种基准，据此可以构建更高级的工具和接口，使数据库开发人员能够编写数据库应用程序。 Java 具有坚固、安全、易于使用、易于理解和可从网络上自动下载等特性，是编写数据库应用程序的杰出语言。所需要的只是 Java应用程序与各种不同数据库之间进行对话的方法。 JDBC可以在各种平台上使用Java，如Windows，Mac OS和各种版本的UNIX。 JDBC API支持用于数据库访问的两层和三层处理模型，但通常，JDBC体系结构由两层组成：

- JDBC API：这提供了应用程序到JDBC管理器连接。
- JDBC驱动程序API：这支持JDBC管理器到驱动程序连接。

#### JDBC的核心组件

JDBC的核心组件包括：

- DriverManager： 此类管理数据库驱动程序列表。使用通信子协议将来自java应用程序的连接请求与适当的数据库驱动程序匹配。
- Driver:此接口处理与数据库服务器的通信，我们很少会直接与Driver对象进行交互。而是使用DriverManager对象来管理这种类型的对象。
- Connection：该界面具有用于联系数据库的所有方法。连接对象表示通信上下文，即，与数据库的所有通信仅通过连接对象。
- Statement：使用从此接口创建的对象将SQL语句提交到数据库。除了执行存储过程之外，一些派生接口还接受参数。
- ResultSet：在使用Statement对象执行SQL查询后，这些对象保存从数据库检索的数据。它作为一个迭代器，允许我们遍历其数据。
- SQLException：此类处理数据库应用程序中发生的任何错误

#### 使用步骤

构建JDBC应用程序涉及以下六个步骤：

- 导入包：需要包含包含数据库编程所需的JDBC类的包。大多数情况下，使用import java.sql.*就足够了。
- 注册JDBC驱动程序：要求您初始化驱动程序，以便您可以打开与数据库的通信通道。
- 打开连接：需要使用DriverManager.getConnection（）方法创建一个Connection对象，该对象表示与数据库的物理连接。
- 执行查询：需要使用类型为Statement的对象来构建和提交SQL语句到数据库。
- 从结果集中提取数据：需要使用相应的ResultSet.getXXX（）方法从结果集中检索数据。
- 释放资源：需要明确地关闭所有数据库资源，而不依赖于JVM的垃圾收集。

建立JDBC连接所涉及的编程可简单概括为以下四个步骤

- 导入JDBC包：将Java语言的*import*语句添加到Java代码中导入所需的类。
- 注册JDBC驱动程序：此步骤将使JVM将所需的驱动程序实现加载到内存中，以便它可以满足您的JDBC请求。
- 数据库URL配置：这是为了创建一个格式正确的地址，指向要连接到的数据库。
- 创建连接对象：最后，调用DriverManager对象的getConnection（）方法来建立实际的数据库连接。

#### Class.forName(); 

注册驱动程序最常见的方法是使用Java的Class.forName()方法，将驱动程序的类文件动态加载到内存中，并将其自动注册。

```java
try {
  Class.forName("com.mysql.cj.jdbc.Driver");
}catch(ClassNotFoundException ex) {
  System.out.println("Error: unable to load driver class!");
  System.exit(1);
}
```

#### 数据库URL配置 

加载驱动程序后，可以使用DriverManager.getConnection（）方法建立连接。为了方便参考，特列出三个重载的DriverManager.getConnection()方法：

- getConnection（String url）
- getConnection（String url，Properties prop）
- getConnection（String url，String user，String password）

#### 创建数据库连接对象

```mysql
String URL = "jdbc:mysql://localhost:3306/dbname?serverTimezone=UTC";
String USER = "username";
String PASS = "password"
Connection conn = DriverManager.getConnection(URL, USER, PASS);
```

完整的连接地址: `jdbc:mysql://127.0.0.1:3306/dbname?useUnicode=true&characterEncoding=UTF8&useSSL=false&serverTimezone=UTC"`

#### 关闭数据库连接 

为确保连接关闭，您可以在代码中提供一个“finally”块。一个finally块总是执行，不管是否发生异常。 要关闭上面打开的连接，你应该调用close（）方法如下:  

```java
conn.close();
```

#### JDBC执行SQL语句

一旦获得了连接，我们可以与数据库进行交互。JDBC Statement和PreparedStatement接口定义了能够发送SQL命令并从数据库接收数据的方法和属性。

#### 创建Statement对象

在使用Statement对象执行SQL语句之前，需要使用Connection对象的createStatement()方法创建Statement的一个实例，如下例所示：

```mysql
 Statement stmt = null;
try {
   stmt = conn.createStatement( );
   . . .
}
   catch (SQLException e) {
   . . .
}
finally {
   . . .
}
```

#### 执行Statement对象 

创建Statement对象后，您可以使用它来执行一个SQL语句，其中有三个执行方法之一。

- boolean execute(String SQL)：如果可以检索到ResultSet对象，则返回一个布尔值true; 否则返回false。使用此方法执行SQL DDL语句或需要使用真正的动态SQL时。
- int executeUpdate(String SQL)：返回受SQL语句执行影响的行数。使用此方法执行预期会影响多个行的SQL语句，例如INSERT，UPDATE或DELETE语句。
- ResultSet executeQuery(String SQL)：返回一个ResultSet对象。当您希望获得结果集时，请使用此方法，就像使用SELECT语句一样。

#### 关闭Statement对象 

就像关闭一个Connection对象以保存数据库资源一样，由于同样的原因，还应该关闭Statement对象。 调用close()方法即可关闭Statement对象。如果先关闭Connection对象，它也会关闭Statement对象。但是，应始终显式关闭Statement对象，以确保正确清理。

```java
Statement stmt = null;
try {
   stmt = conn.createStatement( );
   . . .
}
catch (SQLException e) {
   . . .
}
finally {
   stmt.close();
}
```

#### PreparedStatement 

PreparedStatement的接口扩展了Statement接口，其优点是可以动态地提供参数。如果语句被多次执行，其执行效率比Statement高。

```mysql
PreparedStatement pstmt = null;
try {
   String SQL = "Update Employees SET age = ? WHERE id = ?";
   pstmt = conn.prepareStatement(SQL);
   . . .
}
catch (SQLException e) {
   . . .
}
finally {
   pstmt.close();
} 
```

JDBC中的所有参数都用？符号代替，这被称为参数标记(又叫占位符)。在执行SQL语句之前，必须为每个参数提供值。 用setXXX(参数序号,参数值)方法将值绑定到对应参数，其中XXX代表要绑定到输入参数的值的Java数据类型。如果忘记提供值，将收到一个SQLException。参数的序号从1开始。 记得调用close()方法，显示关闭PreparedStatement对象。

#### ResultSet 

ResultSet对象维护指向结果集中当前行的游标。有多种类型的“游标”，如果没有指定任何ResultSet类型，则取缺省值TYPE_FORWARD_ONLY。

| 类型                              | 类型描述                                                     |
| --------------------------------- | ------------------------------------------------------------ |
| ResultSet.TYPE_SCROLL_INSENSITIVE | 光标可以向前和向后滚动，结果集对创建结果集后发生的数据库的其他更改不敏感。 |
| ResultSet.TYPE_SCROLL_SENSITIVE   | 光标可以向前和向后滚动，结果集对创建结果集之后发生的其他数据库所做的更改敏感。 |
| ResultSet.TYPE_FORWARD_ONLY       | 光标只能在结果集中向前移动。                                 |

#### ResultSet的遍历 

用ResultSet的next()方法取得游标当前行的值，并用getXXX(列名)方法获列这一行某个列的值，XXX是与列匹配的数据类型，next()会推进游标到下一行，如果next()返回false表示数据集已取完(或者是空集)。 示例：

```java
resultSet = statement.executeQuery("select * from student;");while (resultSet.next()) {    System.out.println(resultSet.getString("sno"));    System.out.println(resultSet.getString("sname"));    System.out.println(resultSet.getInt("sage"));} 
```

## 9.2 条件不确定的查询

JDBC的Statement类方法executeQuery(sql)可以执行一条确定的sql语句,如果要执行的sql语句带有变化的部分,比如每个客户的输入的用户名与密码都会不同. 这种情况,有两种解决方案:

#### 把变量直接拼接到sql语句中 

下例中,假定变量userName(类型String)在此前已被赋值:

```mysql
statement = connection.createStatement();
String sql = "select * from user where username = '" + userName + "';";
resultSet = statement.executeQuery(sql); 
```

#### 用PreparedStatement 

用PreparedStatement类,把sql语句中变化的部分当成参数:

```mysql
String sql = "select * from user where username = ? ;";
preparedStatement = connection.prepareStatement(sql);
preparedStatement.setString(1,userName);
resultSet = preparedStatement.executeQuery();
```

## 9.3 JDBC的插入操作

在成功连接数据库后，实例化Statement或PreparedStatement类的一个对象。该对象关联一条sql insert语句，然后调用对象的executeUpdate()方法即可。以PreparedStatement为例：

```mysql
String sql = "insert into user values(?,?)";
pps = connection.prepareStatement(sql);
pps.setString(1,loginName);
pps.setString(2,loginPass);
int n = pps.executeUpdate();
if (n > 0)  {
    System.out.println("执行成功，影响行数：" + n);
} else {
    System.out.println("执行失败.");
} 
```

第2行出现的pps和connection分别为PreparedStatment和Connection类的一个对象(均在该代码段之前已实例化)。第3行和第4行出现的loginName和loginPass是两个String对象，假定此前已被赋值。

## 9.4 事务处理

#### JDBC的事务处理

JDBC缺省情形下，Statement和PreapredStatement均自动为一个事务。因此，当一个Statement调了executeUpdate()方法后，所执行的SQL语句自动提交，其对数据库的修改不可再撤销。

但调用Connection.setAutoCommit(boolean autoCommit)方法可以改变缺省设置： connection.setAutoCommit(false); 将使本会话期内的语句不再自动提交，必须调用Connection的以下方法手动提交：

- commit()
- rollback()

前者为正常提交，后者表示事务回滚，撤销所有修改。

#### JDBC的事务隔离级别

JDBC通过调用Connetion的以下方法设置事务的隔离级别：

```mysql
void setTransactionIsolation(int level)
```

如果没有调用该方法设置隔离级别，将采用DBMS缺省的隔离级别，对MySQL而言，即repeatable read。JDBC支持以下隔离级别：

- static int TRANSACTION_NONE
- static int TRANSACTION_READ_UNCOMMITTED
- static int TRANSACTION_READ_COMMITTED
- static int TRANSACTION_REPEATABLE_READ
- static int TRANSACTION_SERIALIZABLE

它们对应的int值依次为0,1,2,4和8。 不是每个DBMS都支持以上所有的隔离级别。MySQL支持后面4个隔离级别，且缺省状态下为TRANSACTION_REPEATABLE_READ。Oracle支持其中的两个，缺省值为TRANSACTION_READ_COMMITTED。

# 10 数据库设计与实现

## 10.1 从需求分析到逻辑模型

### 业务功能描述

设计一个影院管理系统。影院对当前的放映厅和电影进行排片，顾客到来后，可以购买任一排场的电影票，进入对应放映厅观看。系统中有以下实体集：

1. 电影(movie)：属性有标识号(movie_ID)、电影名(title)、类型(type)、时长(runtime)、首映日期(release_date)、导演姓名(director)、主演姓名(starring)。
2. 顾客(customer)：属性有标识号(c_ID)、姓名(name)、手机号(phone)。
3. 放映厅(hall)：属性有标识号(hall_ID)、放映模式(mode)、容纳人数(capacity)、位置(location)。
4. 排场(schedule)：属性有标识号(schedule_ID)、日期(date)、时间(time)、票价(price)、票数(number)。
5. 电影票(ticket)：属性有标识号(ticket_ID)、座位号(seat_num)。 实体间的关系描述如下： ①. 顾客和电影票有一对多的购买关系。每位顾客可以买多张电影票，每张电影票被一位顾客购买。 ②. 电影票和排场有多对一的属于关系。一张电影票只属于一个排场，一个排场有多张电影票。 ③. 排场和电影有一对多的放映关系。每个排场放一部电影，每部电影可以在多个排场放映。 ④. 排场和放映厅有一对多的位于关系。每个排场位于一个放映厅，每个放映厅可以安排多个排场。

ER图：

![image1](/img/Mysql/1.png)

关系模式：

movie(movie_ID,title,type,runtime,release_date,director,starring),主码:(movie_ID)

customer(c_ID,name,phone),主码:(c_ID)

hall(hall_ID,mode,capacity,location),主码:(hall_ID)

schedule(schedule_ID,date,time,price,number,hall_ID,movie_ID),主码:(schedule_ID);外码:(hall_ID,movie_ID)

ticket(ticket_ID,seat_num,c_ID,schedule_ID),主码:(ticket_ID);外码:(c_ID,schedule_ID)

## 10.2 建模工具简介

**1. ERWIN** erwin Data Modeler行业领先的数据建模解决方案,具有直观的设计和存档功能,支持管理整个企业内任何存储位置的任何数据。但收费较贵，不过，大学学生可以申请erwin Data Modeler Academic Edition，有效期1年。可以去官网下载文档了解具体功能和使用方法： 中文官网：http://www.erwinchina.com/

**2.Navicat** Navicat支持几乎所有你常用的DBMS，支持概念模型，逻辑模型和物理模型，可根据模型文件生成任何DBMS的脚本。支持Forward Engineering和Reverse Engineering。 该软件也是收费。 Navicat同时也是用户较多的DBMS客户端管理工具。 中文官网： https://www.navicat.com.cn/

**3.Microsoft Visio **

现在，Visio已从Office分离出来，需要单独购买。支持陈氏E-R图和Crow's foot。可用来表达概念模型，逻辑模型等。

**4.Draw.io** 这是最容易获得的建模工具，只需要web browser的地址栏里输入URL即可调出： draw.io 或者： https://app.diagrams.net/ 输入前者会自动跳转到后者。 比较适合建立概念模型和逻辑模型。既支持陈氏ER图，也支持Crow's footsER图。但它不能跟具体的DBMS连接,不支持正向及逆向工程。

**5. MySQL Workbench** MySQL社区版自带的免费工具。也是比较好用的图形界面客户端管理工具。支持正向及逆向工具。 官网可下载使用手册。




