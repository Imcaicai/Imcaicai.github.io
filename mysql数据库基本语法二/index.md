# Mysql数据库基本语法(二)


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

## 3.2 使用游标的存储过程

SQL操作都是面向集合的，即操作的对象以及运算的结果均为集合，但有时候，我们需要一行一行地处理数据，这就需要用到游标(CURSOR)，它相当于一个存储于内存的带有指针的表，每次可以存取指针指向的一行数据，并将指针向前推进一行。游标的数据通常是一条查询语句的结果。对游标的操作一般要用循环语句，遍历游标的每一行数据，并将该行数据读至变量，再根据变量的值进行所需要的其它操作。

游标的特点：

- 不可滚动。即只能从前往后遍历游标数据(即从第1行到最后一行)，不能反向遍历，不能跳跃遍历，不能直接访问中间的某一行。
- 只读。游标里的数据只能读取，不能修改。

### 游标的定义与使用

#### 1.DECLARE语句

DECLARE定义的顺序要求：变量→右边→特情处理

变量用来存储从游标读取的数据，根据编程逻辑的需要，可能还要定义其它变量；游标用来存储SELECT语句读取的数据集；当某些特定情形出现时，会自动触发对应的特情处理程序。

定义变量：

```mysql
DECLARE var_name [, var_name] ... type [DEFAULT value]
```

定义游标：

```mysql
DECLARE cursor_name CURSOR FOR select_statement
```

任何合法的select语句(不能带INTO短语)，都可以定义成游标。此后可用FETCH语句读取这个select语句查询到的数据集中的一行数据。

> 注意游标必须定义在变量之后，特情处理程序之前。 
>
> 一个存储过程可义定义多个游标，但不能同名。

定义特情处理的例子：

```mysql
DECLARE handler_action HANDLER
FOR condition_value [, condition_value] ...
statement
handler_action: {
CONTINUE
| EXIT
}
condition_value: {
mysql_error_code
| SQLSTATE [VALUE] sqlstate_value
| condition_name
| SQLWARNING
| NOT FOUND
| SQLEXCEPTION
} 
```

游标应用中至少需要定义一个NOT FOUND的HANDLER(处理例程)：

```mysql
DECLARE CONTINUE HANDLER FOR NOT FOUND SET finished = 1;
```

其含义是当抛出NOT FOUND异常时，置变量finished的值为1,程序继续运行。当然，在此之前，应当先定义变量finished，并初始化为0(也可在循环语句之前初始化为0），finished作为循环的控制变量，仅当finished变成1时，循环结束。

如果特情处理例程由多条语句组成，可以用BEGIN...END组成复合语句。

当一个存储过程中存在多个游标时，对任何一个游标的读取(FETCH)都可能会触发特情处理。比如一个游标的数据被遍历完毕，再试图FETCH下一行时，会触发NOT FOUND HANDLER, 并进而改变某个变量的值，但另一个游标中可能还有未处理完的数据。编程者应当自己想办法区分是哪个游标的数据处理完毕。

#### 2 OPEN语句

```mysql
OPEN cursor_name
```

该语句打开之前定义的游标，并初始化指向数据行的指针(接下来的第一条FETCH语句将试图读取游标的第1行数据)。

#### 3 FETCH语句

```mysql
FETCH [[NEXT] FROM] cursor_name INTO var_name [, var_name] ...
```

ETCH语句读取游标的一行数据到变量列表,并推进游标的指针.关键词NEXT, FROM都可省略(或仅省略NEXT)。注意INTO后的变量列表应当与游标定义中的SELECT列表一一对应(变量个数与SELECT列表个数完全相同，数据类型完全一致，每个变量的取值按SELECT列表顺序一一对应)。

> FETCH一个未打开的游标会出错。

#### 4 CLOSE语句

```mysql
CLOSE cursor_name
```

Close语句关闭先前打开的游标，试图关闭一个未曾打开(OPEN)的游标会出错。

> 没有CLOSE的游标，在其定义的BEGIN...END语句块结束时，将自动CLOSE。

使用游标编写存储过程sp_cursor_demo计算Liverpool足球队在主场获胜的比赛中，上半场的平均进球数，结果通过参数传递。示例程序如下：

```mysql
DELIMITER $$ 
CREATE PROCEDURE sp_cursor_demo(INOUT average_goals FLOAT)
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE matches int DEFAULT(0);
    DECLARE goals int DEFAULT(0);
    DECLARE half_time_goals INT;
    DECLARE team_cursor CURSOR FOR SELECT HTHG FROM epl WHERE (home_team = 'Liverpool') and (ftr = 'H');
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    OPEN team_cursor;
    FETCH team_cursor INTO half_time_goals;
    WHILE NOT DONE DO
      SET  goals = goals + half_time_goals;
      SET  matches = matches + 1;
         FETCH team_cursor INTO half_time_goals;
    END while; 
    SET  average_goals = goals / matches;
    CLOSE team_cursor;
END $$ 
DELIMITER; 
```

存储过程定义后，可通过以下语句定义参数，调用过程，再从返回参数中获取结果:

```mysql
SET @average_goals = 0.0;
CALL sp_cursor_demo(@average_goals);
SELECT @average_goals; 
```

上述带前缀@的变量属于MySQL的用户自定义变量，只在该用户的会话期内有效，对别的用户(客户端)不可见。@前缀变量不用申明变量类型，初始化时，由其值决定其类型。

一般来说，仅当你需要遍历一个数据集，且一次只能处理其中的一行数据时(比如对每一行，要作不同的业务处理)，你才需要使用游标。当游标的数据集较大时，会造成较大的网络时延。使用游标时，应尽可能缩小数据规模(去掉不必要的行和列)。

## 3.3 使用事务的存储过程

### 事务的定义和应用

开启事务：

```mysql
START TRANSACTION 或 BEGIN (前者兼容性更好)
```

事务提交：

```mysql
COMMIT 
```

事务回滚：

```mysql
ROLLBACK 
```

开启或关闭当前会话的自动事务模式：

```mysql
SET autocommit = ON|OFF
```

也可用1|0,true|false代替ON|OFF。 缺省情况下，autocommit模式被设置为ON，即你在命令行提交的每一条语句会自动封装成一个事务，即使下一条语句发生错误，前一条语句产生的结果也不可撤销。 

> 注意，事务内部不允许嵌套另一个事务，尽量不要在事务内部使用DDL语句，因为即使事务回滚,DDL语句对数据库的修改也不会撤销。

# 4 触发器

## 4.1 触发器

触发器是与某个表绑定的命名存储对象，与存储过程一样，它由一组语句组成，当这个表上发生某个操作(insert,delete,update)时，触发器被触发执行。触发器一般用于实现业务完整性规则。当primary key,foreigh key, check等约束都无法实现某个复杂的业务规则时，可以考虑用触发器来实现。

## 4.2 触发器的创建

创建触发器的语句：

```mysql
CREATE TRIGGER trigger_name trigger_time trigger_event 
ON tbl_name 
FOR EACH ROW
trigger_body
```

- trigger_nme: 每个触发器有一个唯一的命名
- trigger_time: 触发的时机，二选一： BEFORE | AFTER
- trigger_event: 触发事件，三选一： INSERT | UPDATE | DELETE
- tbl_name: 与触发器绑定的表
- trigger_body: 触发器程序体，可由变量定义、赋值，流程控制，SQL语句等组成。但触发器体内不能使用create,alter,drop等DDL语句,也不能使用start transaction, commit,rollback等事务相关语句。

> 与创建存储过程、函数一样，创建触发器时也要用delimiter语句重新指定触发器定义语句的界符(触发器内语句的分隔符仍为分号)，在触发器定义之后，再把界符更改回去。

**before与after触发器的区别:**

- before触发器在试图激活触发器的那条语句(insert|delete|update)之前执行。
- after触发器仅在before触发器(如果有的话)和试图激活触发器的那条语句都成功执行后才执行。
- before触发器或after触发器如果未能成功执行，则激活触发器的语句也不会执行。

## 4.3 触发器内的特殊表

在触发器内可以使用两类特殊表：

- old表和new表。它总是与触发器绑定的表有相同的结构，且只能在触发器内访问。
- delete触发器可以访问old表,其内容为被delete掉的数据。
- insert触发器可以访问new表,其内容为insert的新数据。
- update触发器可以访问old表和new表，old表保存着修改前的数据，new表保存着修改后的内容。

