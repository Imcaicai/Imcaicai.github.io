# Mysql数据库基本语法(四)


# 7 并发控制与事务的隔离级别

## 7.1 并发控制与事务的隔离级别

#### 并发操作可能产生的数据不一致性

数据库是共享资源，允许多个用户同时访问同一数据库，特别是在互联网应用成为主流的当下，高可用性、高并发是所有应用追求的目标。但并发操作不加控制，便会产生数据的不一致性。 并发操作可能带来的数据不一致性包括：

- 丢失修改(lost update)
- 读脏数据(dirty read)
- 不可重复读(non-repeatable read)
- 幻读(phantom read)

为解决上述不一致性问题,DBMS设计了专门的并发控制子系统，采用封锁机制进行并发控制，以保证事务的隔离性和一致性(事务是并发控制的基本单位)。

但事务的隔离程度越高，固然一致性--或者説数据的正确性越有保障，但并发度就会越低。很多时候，需要在一致性和并发度间进行取舍，从而就生产了事务的隔离级别的概念。 隔离级别越高，一致性程度越高，并发度越低。反之，隔离级别越低，并发度越高，但代价是会出现某些数据不一致现象。

低隔离级别可以支持更高的并发处理，同时占用的系统资源更少,但可能产生数据不一致的情形也更多一些。

#### **查询事务的隔离级别** 

可用以下语句查询MySQL的事务隔离级别： 

```mysql
select @@GLOBAL.transaction_isolation, @@transaction_isolation;
```

 其中，@@GLOBAL.transaction_isolation全局变量，@@transaction_isolation为本会话期内的变量。通常通过重设该变量的值以改变隔离级别。 上述两个变量的缺省值均为：REPEATABLE-READ，即可重复读。 

#### 设置事务的隔离级别 

以下语句设置事务的隔离级别为可读未提交(read uncommitted): 

```mysql
set session transaction isolation level read uncommitted;
```

如需设置为其它级别，只需替换最后的隔离级别即可。 不同的事务隔离级别意味着不同的封锁协议，程序员只需设置事务的隔离级别即可，其它的交给DBMS并发子系统处理。 不过，MySQL也有lock tables和unlock tables语句，可以直接锁表，另外，MySQL还支持在select语句中使用for share或for update短语主动申请S锁或X锁(只到事务结束才释放)。这样，即使在隔离级别为read uncommitted的情形下，仍有机会保证可重复读，相关内容请参阅MySQL官方文档。

## 7.2 读脏

#### 读脏

读脏(dirty read)，或者又叫脏读，是指一个事务(t1)读取到另一个事务(t2)修改后的数据，后来事务t2又撤销了本次修改(即事务t2以roll back结束)，数据恢复原值。这样，事务t1读到的数据就与数据库里的实际数据不一致，这样的数据被称为“脏”数据，意即不正确的数据。

#### 读脏产生的原因

显然，产生读脏的原因，是事务t1读取数据时，修改该数据的事务t2还没有结束(commit或roll back，统称uncommitted),且t1读取的时间点又恰在t2修改该数据之后。

## 7.3 不可重复读

#### 不可重复读

不可重复读(unrepeatable read)，是指一个事务(t1)读取到某数据后，另一个事务(t2)修改了该，事务t1并未修改该数据，但当t1再次读取该数据时，发现两次读取的结果不一样。

#### 产生不可重复读的原因

显然，不可重复读产生的原因，是事务t1的两次读取之间，有另一个事务修改了t1读取的数据。

根据第一关介绍的基本知识，有两种隔离级别都有可能发生不可重复读。

## 7.4 幻读

#### 幻读(phantom read)

幻读定义其实是有些争议的，在某些文献中，幻读被归为不可重复读(unrepeatable read)中的一类，而另一些则把它与不可重复读区分开来：幻读是指一个事务(t1)读取到某数据后，另一个事务(t2)作了insert或delete操作，事务t1再次读取该数据时，魔幻般地发现数据变多了或者变少了(记录数量不一致)；而不可重复读限指事务t2作了update操作，致使t1的两次读操作读到的结果(数据的值)不一致。

#### 产生幻读的原因

显然，幻读产生的原因，是事务t1的两次读取之间，有另一个事务insert或delete了t1读取的数据集。

除了最高级别serializable(可串行化)以外的任何隔离级别，都有可能发生幻读。

## 7.5 MySQL对共享锁与锁的支持

通过设置不同的隔离级别，以实现不同的一致性与并发度的需求是较通常的作法。但MySQL也提供了主动加锁的机制，使得在较低的隔离级别下，通过加锁，以实现更高级别的一致性。

MySQL的select语句支持for share和for update短语，分别表示对表加共享(Share)锁和写(write)锁，共享锁也叫读锁，写锁又叫排它锁。 下面这条语句，会对表t1加共享锁: 

```mysql
select * from t1 for share;
```

如果select语句涉及多张表，还可分别对不同的表加不同的锁，比如： 

```mysql
select * from t1,t2 for share of t1 for update of t2;
```

加锁短语总是select语句的最后一个短语(复杂的select语句可能有where,group by, having, order by等短语)；不管share还是update锁，都是在事务结束时才释放。当然，锁行为会降低并发度。

## 7.6 可串行化

多个事务并发执行是正确的，当且仅当其结果与按某一次序串行地执行这些事务时的结果相同。两个事务t1,t2并发执行，如果结果与t1→t2串行执行的结果相同，或者与t2→t1串行执行的结果相同，都是正确的(可串行化的)。

如果将事务的隔离级别设置为serializable，则这些事务并发执行，无论怎么调度都会是可串行化的。但这种隔离级别会大大降低并发度，在实践中极小使用。MySQL默认的隔离级别为repeatable read，有的DBMS默认为read committed。



# 8 介质故障与数据库恢复

## 8.1 MySQL的恢复机制

和大多数DBMS一样，MySQL利用备份、日志文件实现恢复。 具体理论知识在此不详细介绍。

## 8.2 MySQL的备份与恢复工具

MySQL提供了以下工具：

- 逻辑备份工具：mysqldump
- 物理备份工具：mysqlbackup(仅限商用版)
- 日志工具：mysqlbinlog
- 还原工具：mysql
- 管理工具：mysqladmin

### **mysqldump** 

逻辑备份工具，它产生一系的SQL语句，执行这些语句可以重建原数据库的所有对象和数据。缺省输出是控制台，可以通过重定向符号，将其产生的SQL语句集合存入到某个文件。 mysqldump可以备份服务器上的全部数据库，也可以指定某些数据库，或者一个数据库中的某些表。

```mysql
mysqldump -h127.0.0.1 -uroot -p123123 [options] --databases db_name
```

--databases 参数用指定数据库名，后面可跟一个或多个数据库的名字，多个数据库名间用空格隔开。 mysqldump命令行工具还可以带若干参数，可选的参数多达几十个，详见官方参考手册。这里只介绍一个：

 --flush-logs 刷MySQL日志，即重新开始一个日志文件。 重新开始一个新的日志文件，对未来确定哪些日志更有用很有帮助。通常海量备份前的日志文件，其重要性会降低许多，因为有备份在手，除非备份文件出故障，你可能不再需要使用之前的日志文件。

#### **mysqlbackup** 

mysqlbackup是MySQL的物理备份工具，只有付费的商用企业版才有。

#### **mysql** 

mysql是MySQL最重要的客户端管理工具，通常用户都是通过mysql登录到MySQL服务器，进行各种操作。此外，还可以直接通过它执行SQL脚本，还原或创建新库。 

```mysql
mysql -h127.0.0.1 -uroot -p12313 < mydb.sql
```

这样会直接执行mydb.sql的脚本。通过mysqldump备份出来的脚本文件，可以用该方法直接用来恢复原数据库。

#### **mysqladmin** 

mysqladmin是MySQL服务器的管理工具，一般用于配置服务器，也可以用来创建或删除数据库： 

```mysql
mysqladmin [options] command [command-arg] [command [command-arg]]
```

常用的command(执行命令)有： 

- create db_name 创建数据库 

- drop db_name 删除数据库 

- flush-logs 刷日志 

- flush-tables 刷表，所有表数据写入磁盘盘 

- kill id,id,...  杀死某些进程 

- password new_password 修改(登录者的)登录密码 

- ping 检查服务器是否可用 

- status 显示服务器状态 

- variables 显示各配置参数的值。

#### **mysqlbinlog** 

mysqlbinlog是MySQL的日志管理工具。在需要手工介入的故障恢复中，该工具必不可少。当然，平常也可以用它查看日志。 

```mysql
mysqlbinlog  mysql-bin.000983
```

上面的例子，用来查看日志文件mysql-bin.000983。MySQL的日志文件具有相同的前缀，后面的数字是日志文件的顺序。这个前缀是可配置的。比如，也可能是binlog.*。

执行日志文件会导致日志所记录的事件重新做一遍，这样可以恢复一个给定时间段的数据，恢复的方法如下: 

```mysql
mysqlbinlog [option] binlog_files | mysql -u root -p
```

介质故障的恢复通常需要把最近一次备份后所有的日志文件全部列上。

mysqlbinlog也支持几十个可选的参数，比如： 

- disable-log-bin 在通过日志恢复数据库期间不再写日志 

- no-defaults 不使用MySQL默认的设置。

