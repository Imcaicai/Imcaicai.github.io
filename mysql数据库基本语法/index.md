# Mysql数据库基本语法


# 1 数据库、表与完整性约束的定义

## 1.1 创建数据库

进入mysql：

```shell
mysql -h127.0.0.1 -uroot -p
```

创建数据库：

```mysql
CREATE DATABASE dbname;
```

指明访问的数据库：

```mysql
use dbname;
```

## 1.2 创建表及表的主码约束

### 建表语法

- CREATE TABLE为保留字，其语义为创建表对象；
- IF NOT EXISTS为可选短语，其语义为仅当该表不存在时才创建表；如果不带该短语，创建表时，如果同名表已存在，则输出报错信息；
- tbl_name为表的名字；
- (列定义|表约束,...)表示表的其它定义都写在一对括号里，括号里为一个或多个“列定义”或“表约束”，如果有多个列的定义或表约束，则它们之间用逗号隔开。

```mysql
CREATE TABLE [IF NOT EXISTS] tbl_name
(列定义|表约束,...)
```

### 列定义语法

- [NOT NULL |NULL]表示空或非空约束，缺省为NULL，即该列的内容允许为空值，NOT NULL则约束该列的内容必须为非空；
- DEFAULT关键字为列指定缺省值，可以是常量，也可以是表达式；
- AUTO_INCREMENT指定该列为自增列(如1，2，3，...)，一般用于自动编号，显然只有数字类型的列才可以定义这一特性；
- [UNIQUE]指定该列值具有唯一性（但可以有空值-甚至多个空值的存在，如果该列没有定义NOT NULL约束）；
- PRIMARY KEY指定该列为主码，相当于定义表的实体完整性约束；只有当主码由单属性组成时，才可以这样定义主码（主码由多属性组成时，应当用表约束来定义）；
- COMMENT用来给列附加一条注释；
- “REFERENCES”短语为该列定义参照完整性约束，指出该列引用哪个表的哪一列的值，以及违背参照完整性后的具体处理规则（多个规则中选一），具体内容将在随后的练习里再讲解；
- CHECK(表达式)为列指定“自定义约束”，只有使（表达式）的值为true的数据才允许写入数据库；关键词CONSTRAINT用来为约束命名。

```mysql
列定义 ::=  列名  数据类型
      [NOT NULL | NULL] 
      [DEFAULT {常量 | (表达式)} ]
      [AUTO_INCREMENT] [UNIQUE [KEY]] [PRIMARY KEY]
      [COMMENT '列备注']
      [REFERENCES tbl_name (col_name)
           [ON DELETE RESTRICT|CASCADE|SET NULL|NO ACTION|SET DEFAULT]
           [ON UPDATE RESTRICT|CASCADE|SET NULL| NO ACTION|SET DEFAULT]]
      [[CONSTRAINT [约束名]] CHECK (表达式)]
```

### 表约束语法

- 主码约束以“PK_”打头，后跟表名，一个表只会有一个主码约束；
- 外码约束以“FK_”打头，后跟表名及列名；
- CHECK约束以“CK_”打头，后跟表名及列名。

```mysql
 表约束 ::= [CONSTRAINT [约束名]]
       | PRIMARY KEY (key_part,...)
       | UNIQUE (key_part,...)
       | FOREIGN KEY (col_name,...) 
        REFERENCES tbl_name (col_name,...)
           [ON DELETE RESTRICT|CASCADE|SET NULL|NO ACTION|SET DEFAULT]
           [ON UPDATE RESTRICT|CASCADE|SET NULL| NO ACTION|SET DEFAULT]
       | CHECK (表达式)
```

主码约束及唯一性约束中“key_part”的语法规则如下：

```mysql
key_part::= {列名| (表达式)} [ASC | DESC]
```

### 定义主码

- 单属性主码，既可在列定义里用PRIMARY KEY约束指定主码，也可以作为表约束单独定义；
- 组合属性作主码时，该主码只能定义为表约束。

表创建好之后可以使用如下语句**列出所有的表**：

```mysql
show tables;
```

还可以使用如下语句**查看表的结构**，用来检查所建的表是否正确体现了原意： 

```mysql
DESC 表名;
```

## 1.3 创建外码约束

### 外码

外码是表中的一个或一组字段（属性），它可以不是本表的主码，但它与某个主码(同一表或其它表的主码)具有对应关系（语义完全相同）。外码可以是一列或多列，一个表可以有一个或多个外码。当我们谈论外码时，一定有个主码与它对应，外码不可能单独存在。主码所在的表为主表，又称父表，外码所在的表为从表，又称子表。

### 外码约束

外码用来在数据之间（即外码与其对应的主码间）建立关联。参照完整性约束用于约束外码列的取值范围：外码列的取值要么为空，要么等于其对应的主码列的某个取值。在语义允许，又不违反其它约束规则的情形下，外码列的取值才可以为空。

### 定义外码约束

可在定义表的同时定义各种完整性约束规则(当然包括外码约束，亦即参照完整性约束）。外码约束既可以定义为列约束，亦可定义为表约束。

列级外码约束的语法格式如下：

```mysql
列级外码约束 ::=  列名  数据类型      
	[REFERENCES tbl_name (col_name)      
	[ON DELETE RESTRICT|CASCADE|SET NULL|NO ACTION|SET DEFAULT]     [ON UPDATE RESTRICT|CASCADE|SET NULL|NO ACTION|SET DEFAULT]
```

表约外码约束的语法格式如下：

```mysql
表级外码约束 ::= [CONSTRAINT [约束名]]
        FOREIGN KEY (col_name,...) 
        REFERENCES tbl_name (col_name,...)
           [ON DELETE RESTRICT|CASCADE|SET NULL|NO ACTION|SET DEFAULT]
           [ON UPDATE RESTRICT|CASCADE|SET NULL| NO ACTION|SET DEFAULT] 
```

MySQL表级外码约束的好处是可以给约束命名，且支持多属性组合外码（即外码由多个列组成）。**事实上，外码约束定义在表一级，是不二的选择，因为MySQL对列级外码约束的支持仅停留在语法检查阶段，实际并没有实现(至少8.0.22还没有实现）。**外码约束的名称一般以“FK_”为前缀，这是约定俗成的规则。

## 1.4 check约束

### 用户定义的完整性约束

关系数据库的完整性约束共有三类：实体完整性约束，参照完整性约束以及用户定义的完整性约束。实体完整性约束和参照完整性约束分别用PRIMARY KEY和FOREIGN KEY来实现；CHECK约束是最主要的一类用户定义的完整性约束，用于定义用户对表中的某列的数据约束，或表中一行中几列之间应该满足的完整性约束。

### CHECK约束的定义方法

- 如果约束仅涉及单个列，则该约束既可以定义为列约束，也可以定义为表约束，例如：“性别”列的取值仅限从（“男”,“女”)中取值；
- 如果约束涉及表的多个列，则该约束只能定义为表约束，例如：如果职称为“教授”，则它的薪资应当不低于6000元。这个约束涉及到“职称”和“薪资”两个列的内容，故只能用表约束来实现。

CHECK约束的语法: 

```mysql
CHECK约束 ::= [CONSTRAINT [约束名]] CHECK (条件表达式)]
```

只有当条件表达式的值为true时，数据（插入的新数据，或修改后的数据）才会被接受，否则将被拒绝。

## 1.5 DEFAULT约束

默认值约束(Default约束)用于给表中的字段指定默认值，即往表里插入一条新记录时，如果没有给这个字段赋值，那么DBMS就会自动赋于这个字段默认值。

Default约束只能定义为列一级约束，即在需要指定默认值的列之后用关键字DEFAULT申明默认值，其语法为：

```mysql
col_name data_type [DEFAULT {literal | (expr)} ]
```

即在列名与列的数据类型之后申明Default约束。当然Default约束只是众多列约束中的一种，该列可能还有NOT NULL, UNIQUE, AUTO_INCREMENT, CHECK，FOREIGN KEY等其它约束。

DEFAULT关键字引出的默认值可以是常量，也可以是一个表达式。

### 举例

- AUTO_INCREMENT约束仅用于整数列;
- DEFAULT约束指定默认值为表达式时，表达式要写在一对括弧里;
- 这里，curdate()是MySQL的系统函数，其功能是取当前日期;
- 语句中，表名称order前后的符号是必须的，因为order是MySQL的关键字，当表名或列名与关键字冲突时，名称前后必须加`号。

```mysql
create table `order`(   
	orderNo int auto_increment primary key,    
	orderDate date default (curdate()),    
	customerNo char(10),    
	employeeNo char(10));
```

## 1.6 UNIQUE约束

### UNIQUE约束

跟主码(Primary Key)约束一样，Unique约束既可以是对单属性的约束，也可以是对属性组约束，具有Unique约束的属性或属性组的取值必须是唯一的，否则就违反了Unique约束。不过，跟主码不同的是，Unique约束并不要求字段必须非空(Not Null)，所以，实际上，它只能约束非空的属性(组）取值是唯一的。同时具有Not Null约束的Unique属性(组)相当于候选码。一个表只能定义一个主码约束，但可以定义多个Unique约束。

### UNIQUE约束的语法

跟主码约束一样，单字段的Unique约束既可定义为列约束，亦可定义为表约束，组合字段的Unique约束只能定义为表约束。

Unique列约束的语法为： 

```mysql
col_name data_type UNIQUE
```

Unique表约束的语法为：

```mysql
[CONSTRAINT [约束名]] UNIQUE(列1, 列2, ...)
```

Constraint短语可以省略。既使写上关键词constraint，也可以省略约束名。约束未命名时，MySQL将按一定规则自动予以命名。

NOT NULL只能作列约束，且不用命名。UNIQUE约束作列约束时不能自主命名，作表约束时可以自主命名。

# 2 表结构与完整性约束的修改

## 2.1 修改表名

### ALTER TABLE语句

Alter Table语句用于修改由Create Table语句创建的表的结构。比如，添加或删除列，添加或删除约束，创建或销毁索引，更改列的数据类型，更改列名甚至表名等。

```mysql
ALTER TABLE 表名
[修改事项 [, 修改事项] ...]
```

常用修改事项有：

- 用ADD关键词添加列和约束(主码、外码、CHECK、UNIQUE等约束)；
- 用DROP关键词删除列、约束和索引(含Unique)；
- 用MODIFY关键词修改列的定义(数据类型和约束)；
- 用RENAME关键词修改列、索引和表的名称；
- 用CHANGE关键词修改列的名称，同时还可以修改其定义(类型和约束)。

```mysql
修改事项 ::=
    ADD [COLUMN] 列名 数据类型 [列约束]
       [FIRST | AFTER col_name]
  | ADD {INDEX|KEY} [索引名] [类型] (列1,...) 
  | ADD [CONSTRAINT [约束名]] 主码约束
  | ADD [CONSTRAINT [约束名]] UNIQUE约束
  | ADD [CONSTRAINT [约束名]] 外码约束
  | ADD [CONSTRAINT [约束名]] CHECK约束
  | DROP {CHECK|CONSTRAINT} 约束名
  | ALTER [COLUMN] 列名 {SET DEFAULT {常量 | (表达式)} | DROP DEFAULT}
  | CHANGE [COLUMN] 列名 新列名 数据类型 [列约束]
        [FIRST | AFTER col_name]
  | DROP [COLUMN] 列名
  | DROP {INDEX|KEY} 索引名
  | DROP PRIMARY KEY
  | DROP FOREIGN KEY fk_symbol
  | MODIFY [COLUMN] 列名 数据类型 [列约束]
        [FIRST | AFTER col_name]
  | RENAME COLUMN 列名 TO 新列名
  | RENAME {INDEX|KEY} 索引名 TO 新索引名
  | RENAME [TO|AS] 新表名
```

说明：

1. 注意`RENAME`,`MODIFY`和`CHANGE`的区别：仅改列名，用`RENAME`; 只改数据类型不改名，用`MODIFY`; 既改名又改数据类型，用`CHANGE`。
2. 在用MODIFY,CHANGE更改列的数据类型和约束时，修改后的CHECK约束并不会生效(MySQL只作语法检查，并未实现代码--至少MySQL 8.0.22还未实现)。但用ADD新增列的CHECK约束，是有效的。
3. 删除主码约束只能用`Drop Primary Key`短语,不能使用`drop constraint`短语，即便在创建主码约束时显式命名了该主码约束。试图使用“drop constraint 主码约束名”短语删除主码，会给出错误提示，显示该约束并不存在。因为MySQL并没有完全实现“constraint 约束名 primary key(...)”短语的功能，仅作了语法检查，然后直接忽略了主码约束的命名。
4. 给已有列增加Default约束，可用 alter 列 `set default ...` 短语；删除列的default约束，可用 `alter 列 drop default` 短语。当然，也可以用 `Modify 列名 数据类型 ...` 短语。如果该短语没有default约束，就相当于删除了原来的default约束，如果该短语带有default约束，就相当于添加了default约束，如果之前已有default约束，则新的Default约束将代替原有的Default约束;
5. 删除unique约束，既可用 `drop constraint 约束名` 短语，也可以用 `drop key 索引名` 短语来实现,唯一性(unique)约束实际是用`Unique`索引来实现的，Unique索引的名字总是与Unique约束名完全一样，它们本就是同一套机制。如果没有显式命名的话，Unqiue索引名或者说Unique约束名一般与列同名(组合属性作索引，则与组合属性中的第1列同名)。但要注意是的，在更改列名后，Unique索引名并不会随之更改。在创建Unqiue约束时，用“constriant”短语给约束取一个有意义的名字，是一个值得推荐的习惯。

### 更改表名

```mysql
alter table 表名 rename [TO|AS] 新表名
```

## 2.2 添加与删除字段

### 给表添加字段

- 关键字FIRST指示新添加的列为第1列；
- AFTER指示新添加的列紧跟在指定列的后面。
- 如果省略位置指示，则新添加的列将成为表的最后一列。
- 关键字column可以省略。

```mysql
ALTER TABLE 表名 ADD [COLUMN] 列名 数据类型 [列约束]  [FIRST | AFTER 列名]
```

举个例子：

```mysql
alter table student 
add mobile char(11) constraint CK_student_mobile check(mobile rlike '1[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]');
```

check约束中的rlike还可以用regexp替代，它们是同义语。跟Oracle一样，MySQL用正则匹配表达式来测试字段值是否符合某个pattern，rlike比like关键词所支持的功能要强大得多。

### 删除表中的字段

关键字COLUMN可以省略，其语法格式为：

```mysql
 ALTER TABLE 表名 DROP [COLUMN] 列名 
```

举个例子：

在学生档案里记录年龄的作法并不科学，因为年龄会随着时间的变化而变化，档案里记录17岁，还得根据当年记录的日期以及当下的日期推算实际年龄。替代方案是记录出生日期而不是年龄。解决方案：

```mysql
# 第1步：添加列DOB
alter table student add DOB date after sex;
# 第2步，根据age推算DOB
update student set DOB = date_add('2020-9-1', interval -1*age year);	# date_add()是mysql的函数
select * from student;	# 查看表student的内容
# 第3步，删除列age
alter table student drop age;
```

## 2.3 修改字段

修改列名、列数据类型和列约束，以及列序的修改事项有：

```mysql
修改事项 ::=
   ALTER [COLUMN] 列名 {SET DEFAULT {常量 | (表达式)} | DROP DEFAULT}
  | CHANGE [COLUMN] 列名 新列名 数据类型 [列约束]
        [FIRST | AFTER col_name]
  | MODIFY [COLUMN] 列名 数据类型 [列约束]
        [FIRST | AFTER col_name]
  | RENAME COLUMN 列名 TO 新列名
```

- CHANGE短语可修改列名、数据类型和列约束；
- MODIFY短语可修改列的数据类型和约束；
- RENAME短语仅用于更改列名；
- ALTER短语仅用于修改列的DEFAULT约束或删除列的DEFAULT约束。
- CHANGE和MODIFY短语还可以修改列在表中的位置。

```mysql
create database MyDb;
use MyDb;
create table s(
    sno char(10) primary key,
    name varchar(32) not null,
    ID char(18) unique
);
```

### 修改字段名称

注意：关键字COLUMN不能省略

```mysql
ALTER TABLE 表名 RENAME COLUMN 列名 TO 新列名
```

如果修改列名的同时，还要修改列的类型和约束，则用CHANGE短语：

```mysql
ALTER TABLE 表名 CHANGE [COLUMN] 列名 新列名 数据类型 [列约束] [FIRST | AFTER col_name]
```

如果新列带有CHECK约束的话，MySQL只会对这个约束作语法检查，并不会去实现这个约束，其它类型的约束没有问题。如果真有这样的需求，不如先DROP之前的列，再ADD新的列，新列附带的CHECK约束是会被实现的。

### 修改字段的数据类型和约束

如果列名称不变，仅需要修改其数据类型和约束，则用MODIFY短语： 

```mysql
ALTER TABLE 表名 MODIFY [COLUMN] 列名 数据类型 [列约束]  [FIRST | AFTER col_name]
```

> 注意，一旦使用MODIFY短语修改列，则该列之前的数据类型、约束将被新的数据类型和约束取而代之。如果之前定义了列约束，修改后不带列约束，相当于删除了之前的约束。

如果需要修改(或添加)列的DEFAULT约束，则既可用上面的MODIFY短语，也可以使用ALTER短语： 

```mysql
ALTER TABLE 表名 ALTER [COLUMN] 列名 SET DEFAULT {常量 | (表达式)}
```

删除列的DEFAULT约束，则可以使用ALTER短语(或MODIFY短语)：

```mysql
ALTER TABLE 表名 ALTER [COLUMN] 列名 DROP DEFAULT
```

### 修改字段在表中的位置

如果仅需修改列在表中的位置，仍用MODIFY短语： 

```mysql
ALTER TABLE 表名 MODIFY [COLUMN] 列名 数据类型 [列约束]  [FIRST | AFTER col_name]
```

举个例子：

```mysql
alter table resident modify idNo char(18),
       modify height int unsigned,
       rename column educationalBackground to education;
```

## 2.4 添加、删除与修改约束

### 主码的添加与删除

删除主码：

```mysql
ALTER TABLE 表名 DROP PRIMARY KEY;
drop index `PRIMARY` on 表名;
```

添加主码：

```mysql
ALTER TABLE 表名 ADD [CONSTRAINT [约束名]] PRIMARY KEY(列1,列2,...);
```

> MySQL尽管在语法上支持主码约束的命名，但实际上并没有真正实现主码约束的命名功能。即，MySQL并不会创建用户语句中所指定的约束名。所以，试图通过约束名删除主码约束是行不通的。
>
> MySQL中，所有的主码约束(主码索引)名均为PRIMARY，无论怎么命名或更命，这个名字都不会改变。由于PRIMARY是MySQL的保留字，所以，在引用这个主码约束(索引)名时，必须用一对``符号将PRIMARY括起来。

举个例子：

```mysql
# 第1步：删除错误的主码定义
alter table score drop primary key;
# 第2步：重新创建主码
alter table score add constraint PK_score primary key(sno,cno); 
alter table score add primary key(sno,cno);

# 进阶版
alter table score drop primary key, add primary key(sno,cno);
```

### 外码的删除与添加

删除外码：

```mysql
ALTER TABLE 表名 DROP CONSTRAINT 约束名
ALTER TABLE 表名 DROP FOREIGN KEY 约束名
```

添加外码：

```mysql
ALTER TABLE 表名 ADD [CONSTRAINT [约束名]] 外码约束
```

> 约束名是可选的，如果省略命名短语，MySQL将按一定的规则自动命名。将来如果要删除该约束，必须先查询到该约束的名字（注：从MySQL的数据字典查询）。

创建外码时，MySQL将同步创建外码索引，如果外码约束有显式命名，则外码索引与外码约束同名。如果外码约束未命名，则外码索引与外码列的列名同名。

删除外码约束时，外码索引不会跟着删除。如果将来重新创建了外码，并显式命名，则外码索引会自动更名(与外码约束名保持相同)。

### CHECK约束的删除与添加

删除check约束：

```mysql
ALTER TABLE 表名 DROP CONSTRAINT 约束名
```

添加check约束：

```mysql
ALTER TABLE 表名 ADD [CONSTRAINT [约束名]] check(条件表达式)
```

> 添加约束时，如果现有数据与该约束规则相矛盾，则创建约束的请求会被拒绝。

### UNIQUE约束的添加与删除

删除Unique约束：

```mysql
alter table 表名 drop constraint 约束名;
drop index 索引名 on 表名;
```

添加Unique约束： 

```mysql
alter table 表名 ADD [CONSTRAINT [约束名]] UNIQUE(列1,...)
```

创建unique约束时，将同步创建unique索引，索引名与约束同名。如果未显式命名unique约束或索引，MySQL将按一定规则自动命名（单列的unique索引或约束与列同名）。

约束的修改一般通过先删除旧约束再重建新约束来实现。

# 3存储过程与事务

## 3.1 使用流程控制语句的存储过程

### 变量的定义与赋值

用`declare`语句定义变量，并赋予默认值或初始值，未赋默认值则初始值为null：

```mysql
DECLARE var_name [, var_name] ... type [DEFAULT value]
```

用`set`语句给变量赋值，set语句还可以设置许多MySQL的配置参数。

```mysql
SET variable = expr [, variable = expr]
```

通过`select`语句给变量赋值，select语句可以带复杂的where，group by，having等短语。

```mysql
select col into var_name from table; #将table表中的col列值赋给变量
```

### 复合语句与流程控制语句

复合语句BEGIN...END

```mysql
BEGIN
[statement_list]
END; 
```

if语句

```mysql
IF search_condition THEN  statement_list
[ELSEIF search_condition THEN statement_list] ...
[ELSE statement_list]
END IF; 
```

while语句

```mysql
WHILE search_condition DO
   statement_list
END WHILE; 
```

### 存储过程的定义

**存储过程**是一种在数据库中存储复杂程序，以便外部程序调用的一种数据库对象。

存储过程是为了完成特定功能的 `SQL` 语句集，经编译创建并保存在数据库中，用户可通过指定存储过程的名字并给定参数（需要时）来调用执行。

存储过程思想上很简单，就是数据库 `SQL` 语言层面的代码**封装与重用**，即具有名字的一段代码，用来完成一个特定的功能。

### 存储过程的创建和查询

创建存储过程：

```mysql
create procedure 存储过程名(参数) 
```

每个存储的程序都包含一个由 `SQL` 语句组成的主体。此语句可能是由以分号（`;`）字符分隔的多个语句组成的复合语句。例如：

```mysql
CREATE PROCEDURE proc1()
BEGIN
SELECT * FROM user;
END;
```

`MySQL` 本身将分号识别为语句分隔符，因此必须临时重新定义分隔符以使 MySQL 将整个存储的程序定义传递给服务器。

要重新定义 `MySQL` 分隔符，请使用 `delimiter`命令。使用 `delimiter` 首先将结束符定义为`//`，完成创建存储过程后，使用`//`表示结束，然后将分隔符重新设置为分号（`;`）：

```mysql
DELIMITER //
CREATE PROCEDURE proc1()
BEGIN
SELECT * FROM user;
END //
DELIMITER ;
```

`/`也可以换成其他符号，例如`$`;

执行存储过程：

```mysql
call 存储过程名
```

存储过程的参数：

- `IN`：输入参数，也是默认模式，表示该参数的值必须在调用存储过程时指定，在存储过程中修改该参数的值不能被返回；
- `OUT`：输出参数，该值可在存储过程内部被改变，并可返回；
- `INOUT`：输入输出参数，调用时指定，并且可被改变和返回。

### 存储过程的查询和删除

查询存储过程：

```mysql
SHOW PROCEDURE STATUS WHERE db='数据库名';
```

查看存储过程的详细定义信息：

```mysql
SHOW CREATE PROCEDURE 数据库.存储过程名;
```

删除存储过程：

```mysql
DROP PROCEDURE [IF EXISTS] 数据库名.存储过程名;
```



