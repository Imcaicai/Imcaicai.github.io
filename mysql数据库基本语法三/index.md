# Mysql数据库基本语法(三)


# 5 用户自定义函数

函数其实有多种，比如标量函数(仅返回一个值)和表函数(返回结果是表),语法也各不相同。这里，我们仅给出一个简化的创建标量函数的语法:

```mysql
create function function_name([para data_type[,...]])
returns data_type
begin
    function_body;
    return expression; 
end
```

- function_name:函数名；
- para:参数名；
- data_type:参数的数据类型；
- 一个函数可以没有参数，也可以有多个。多参数间用逗号分隔。
- function_body:函数体。即由合法的SQL语句组成的程序段。
- expression:函数返回值，可以是常量、表达式，甚至是一条select语句查询的值（必须保证结果唯一);该值类型应与returns短语定义的类型相同。

函数一旦定义，就可以像内部函数一样使用，比如出现在select列表、表达式、以及where子句的条件中。

> MySQL的函数定义与存储过程的定义一样，在定义函数之前要用“delimiter 界符”语句指定函数定义的结束界符，并在函数定义后，再次使用“delimiter 界符”语句恢复MySQL语句的界符(分号)。



# 6 安全性控制

与大多数商用DBMS一样，MySQL采用自主存取控制(DAC)机制进行安全性管理。通过用户，数据对象，权限，授权，收回权限等要素进行存取控制。另外，为了方便批量授权给同一类用户，引入了角色。

## 6.1 用户（User）

MySQL创建用户的语句：

```mysql
create user 用户名 identified by 用户登录密码；
```

通常用户名可包含域名，限定用户在该域名内登录再有效。例：

```mysql
CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
```

该语句创建用户jeffrey,密码为'password'，仅限在MySQL服务器本机上登录才有效。用户名与域合起来，被称为账户(account)。 注意不要写成：'jeffrey@localhost'，它代表账户： 'jeffrey@localhost'@'%' 意即用户名为jefrrey@localhost，在任何机器上登录都有效。两者的含义完全不同。

drop user语句可删除用户。用户被删除时，该用户拥有的权限自动被收回。 

alter user语句可重置用户密码：

```mysql
ALTER USER user IDENTIFIED BY 'new_password';
```

MySQL在安装时，初始用户名为root，此为系统管理员用户，其余用户均由root创建，并授权。经授权的用户也可以创建用户。

## 6.2 权限

MySQL常用的权限有：

- all: 所有权限(grant option除外)
- alter: alter table权限
- alter routine: alter 存储过程
- create: create database/table
- create role: create role
- create foutine: create 存储过程和函数
- create user： create/alter/rename/drop user
- create view: create view
- delete: delete语句
- drop: drop database/table
- drop role: drop role
- execute: 调用存储过程或函数
- index：create/drop index
- insert: insert语句
- select: select语句
- trigger: 触发器相关操作
- update: update语句 等。 select,update,insert,delete还可以用在列上，如select(c_id),update(b_balance)等。

## 6.3 角色

角色是权限的集合。如果有一组人(承担相同职责的小组,或者説小组成员扮演相同的角色)应该被授予一组相同的权限，不妨创建一个角色，将那组权限授予该角色，然后再将角色授予该组的每个成员。这比一个个地给每个组员授予一批权限要方便得多。

创建角色的语句：

```mysql
CREATE ROLE [IF NOT EXISTS] role [, role ] ...
```

一次可以创建多个角色。 删除角色： 

```mysql
DROP ROLE [IF EXISTS] role [, role ] ... 
```

角色被删除后，拥有该角色的用户立即失去角色定义的权限组合。不过，如果用户同时拥有多个角色，两个角色代表的权限集合如果有交集，则该用户仍拥有交集代表的权限。

## 6.4 GRANT授权语句

以下语句授予权限给用户或角色： 

```mysql
grant 权限[,权限] ... on 数据库对象 to user|role,[user|role]... [with grant option]
```

可以同时将多个权限授予多个用户或角色。 with grant option表示被授权用户可以传播权限，即授权该用户将其拥有的权限（之前获得的权限，通过本语句获得的权限，以及今后获得的权限）再授予其它用户。

以下语句授予角色所代表的权限集给用户或角色： 

```mysql
GRANT role [, role] ... TO user_or_role [, user_or_role] ... [WITH ADMIN OPTION]
```

总之，GRANT语句可以将权限或角色（权限集合）授予用户或角色。但是不能将权限和角色混合授予用户(或角色)。不过，你可以分开用两条不同的GRANT语句来实现：直接授权语句有关键词ON，间接授权(角色代表的权限集合)语句不带ON关键词。

## 6.5 REVOKE收回权限语句

以下语句将对象的权限从用户或角色手中收回： 

```mysql
revoke 权限[,权限]... on 数据库对象 from user|role[,user|role]...
```

下列语句把role所代表的权限集合从用户或角色中收回：

```mysql
REVOKE role [, role ] ... FROM user_or_role [, user_or_role ] ...
```

如果用户本身拥有多个角色所代表的权限集合，而这些集合存在交集，收回其中部分角色代表的权限集后，用户可能仍拥有那个角色所代表的部分权限(交集代表的那部分权限)。

