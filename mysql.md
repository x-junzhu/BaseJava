## MySql

## 1. mysql简介 

### 1.1 mysql 架构

+ 连接层

​      最上层是一些客户端和连接服务，包含本地socket通信和大多数基于客户端/服务端工具实现的类似于tcp/ip 的
通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证
安全接入的客户端提供线程。同样在该层上可以实现基于SSL 的安全链接。

+ 服务层

  + Management Serveices & Utilities：系统管理和控制工具
  + SQL Interface：SQL 接口。接受用户的SQL 命令，并且返回用户需要查询的结果。比如select from
    就是调用SQL Interface。
  + Parser：解析器。SQL 命令传递到解析器的时候会被解析器验证和解析。
  + Optimizer：查询优化器。SQL 语句在查询之前会使用查询优化器对查询进行优化，比如有
    where 条件时，优化器来决定先投影还是先过滤。
  + Cache 和Buffer：查询缓存。如果查询缓存有命中的查询结果，查询语句就可以直接去查询缓存中取
    数据。这个缓存机制是由一系列小缓存组成的。比如表缓存，记录缓存，key 缓存，
    权限缓存等。
+ 引擎层

​      存储引擎层，存储引擎真正的负责了MySQL 中数据的存储和提取，服务器通过API 与存储引擎进行通信。不同
的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取。

+ 存储层

​      数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

>  Linux开启和关闭mysql服务：service mysql start, service mysql stop

### 1.2 mysql查询大致流程

mysql 的查询流程大致是：
      mysql 客户端通过协议与mysql 服务器建连接，发送查询语句，先检查查询缓存，如果命中，直接返回结果，
否则进行语句解析,也就是说，在解析查询之前，服务器会先访问查询缓存(query cache)——它存储SELECT 语句以及相应的查询结果集。如果某个查询结果已经位于缓存中，服务器就不会再对查询进行解析、优化、以及执行。它仅仅将缓存中的结果返回给用户即可，这将大大提高系统的性能。
      语法解析器和预处理：首先mysql 通过关键字将SQL 语句进行解析，并生成一颗对应的“解析树”。mysql 解析
器将使用mysql 语法规则验证和解析查询；预处理器则根据一些mysql 规则进一步检查解析数是否合法。查询优化器当解析树被认为是合法的了，并且由优化器将其转化成执行计划。一条查询可以有很多种执行方式，
      最后都返回相同的结果。优化器的作用就是找到这其中最好的执行计划。然后，mysql 默认使用的BTREE 索引，并且一个大致方向是:无论怎么折腾sql，至少在目前来说，mysql 最多只用到表中的一个索引。

### 1.3 mysql基本设置

> 字符集

①查看当前字符集

show variables like '%char%';

②修改当前数据库配置文件

```mysql
[mysqld]
# 设置mysql的安装⽬目录
basedir=D:\developerSetting\mysql-5.7.29\
# 设置mysql数据库的数据的存放⽬目录
datadir=D:\developerSetting\mysql-5.7.29\data\
# 设置mysql的端⼝口号
port = 3306
#skip-grant-tables
# 设置字符集编码(支持中文)
character_set_server=utf8
[mysql]
default-character-set=utf8
[client]
default-character-set=utf8
```

> show profile 查看sql执行周期

查看profile是否开启 show variables like '%profiling%';

如果没有开启，可以执行set profiling=1 开启,然后可以使用**show profiles**查看最近执行SQL语句。



### 1.4 SQL执行顺序

手写SQL的执行顺序：

```sql
select distinct
		< select_list >
from 
		< left_table > < join_type >
join < right_table >  on < join_condition >
where
		< where_condition >
group by
		< group_by_list >
having
		< having_condition >
order by 
		< order_by_condition >
limit
		< limit_number >
```

真正执行的顺序：
	随着Mysql 版本的更新换代，其优化器也在不断的升级，优化器会分析不同执行顺序产生的性能消耗不同而动
态调整执行顺序。下面是经常出现的查询顺序：



![avatar](picture/mysql_order.png)

![avatar](picture/mysql_order3.png)

### 1.5 MyIASM和InnoDB

| 对比项         | MyIASM                                                 | InnoDB                                                       |
| -------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 外键           | 不支持                                                 | 支持                                                         |
| 事务           | 不支持                                                 | 支持                                                         |
| 行表锁         | 表锁，即使操作一条记录也会锁住整个表，不适合高并发操作 | 行锁，操作时只锁住一行，不对其他行有影响，**适合高并发操作** |
| 缓存           | 只缓存索引，不缓存真实数据                             | 不仅缓存索引还要缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 关注点         | 读性能                                                 | 并发写、事务、资源                                           |
| 默认安装       | Y                                                      | Y                                                            |
| 默认使用       | N                                                      | Y                                                            |
| 自带系统表使用 | Y                                                      | N                                                            |

> 常用命令

①show engines:查看所有的数据库引擎

②show variables like '%storage_engine%' 查看默认的数据库引擎

## 2. SQL基本语句

### 2.1 7种Join理论

![avatar](picture/sql_join.png)

### 2.2 建表语句

```sql
CREATE TABLE `t_dept` (
`id` INT(11) NOT NULL AUTO_INCREMENT,
`deptName` VARCHAR(30) DEFAULT NULL,
`address` VARCHAR(40) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

CREATE TABLE `t_emp` (
`id` INT(11) NOT NULL AUTO_INCREMENT,
`name` VARCHAR(20) DEFAULT NULL,
`age` INT(3) DEFAULT NULL,
`deptId` INT(11) DEFAULT NULL,
empno int not null,
PRIMARY KEY (`id`),
KEY `idx_dept_id` (`deptId`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

INSERT INTO t_dept(deptName,address) VALUES('华山','华山');
INSERT INTO t_dept(deptName,address) VALUES('丐帮','洛阳');
INSERT INTO t_dept(deptName,address) VALUES('峨眉','峨眉山');
INSERT INTO t_dept(deptName,address) VALUES('武当','武当山');
INSERT INTO t_dept(deptName,address) VALUES('明教','光明顶');
INSERT INTO t_dept(deptName,address) VALUES('少林','少林寺');

INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('风清扬',90,1,100001);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('岳不群',50,1,100002);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('令狐冲',24,1,100003);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('洪七公',70,2,100004);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('乔峰',35,2,100005);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('灭绝师太',70,3,100006);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('周芷若',20,3,100007);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('张三丰',100,4,100008);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('张无忌',25,5,100009);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('韦小宝',18,null,100010);
```

### 2.3 案例

```sql
# 1.所有有门派人员的信息（要求显示门派名称）内连接
select e.name, d.deptName from t_emp e inner join t_dept d on e.deptId=d.id;
# 2.列出所有人员及其门派信息
select e.id, e.name, d.deptName from t_emp e left join t_dept d on e.deptId=d.id;
# 3.所有无门派人士
select * from t_emp where deptId is null;
# 4.所有无人的门派
select d.* from t_dept d left join t_emp e on d.id=e.deptId where e.deptId is null;
# 5.所有人员和门派的对应关系
select * from t_emp e left join t_dept d on e.deptId=d.id 
union 
select * from t_emp e right join t_dept d on e.deptId=d.id;
# 6.所有没有入门派的人员和没人入的门派
select * from t_emp e left join t_dept d on e.deptId=d.id where e.deptId is null
union
select * from t_dept d left join t_emp e on e.deptId=d.id where e.deptId is null;
# 7.添加CEO字段
alter table t_dept add CEO INT(11);
update t_dept set CEO=2 where id=1;
update t_dept set CEO=4 where id=2;
update t_dept set CEO=6 where id=3;
update t_dept set CEO=8 where id=4;
update t_dept set CEO=9 where id=5;
# 8.求各个门派对应的掌门人名称
select d.deptName, e.name from t_dept d left join t_emp e on e.id=d.CEO where e.name is not null;
# 9.求所有当上掌门人的平均年龄
select AVG(e.age) from t_dept d left join t_emp e on e.id=d.CEO;
# 10.求所有人物对应的掌门名称
select ed.name as employee,c.name as boss 
from (select e.name,d.ceo from t_emp e left join t_dept d on e.deptId=d.id) ed 
left join t_emp c on ed.ceo=c.id;
```

## 3. 索引优化分析

### 3.1 索引的概念

>  什么是索引？

​		MySQL官方对索引的定义为：索引（index)是帮助MySQL高效获取数据的数据结构。可以得到索引的本质：索引是数据结构，可以理解为**排好序的快速查找数据结构。**

> 优缺点

优点：

+ 提高数据检索的效率，降低数据库的IO成本。
+ 通过索引列对数据进行排序，降低数据排序的成本，降低了CPU的消耗。

缺点：

+ 虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为
  更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为
  更新所带来的键值变化后的索引信息。
+ 实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录，所以索引列也是要占用空间
  的。

### 3.2 MySQL索引

> BTree索引

+ 叶子节点具有相同的深度，叶节点的指针为空
+ 所有索引元素不重复
+ 节点中的数据索引从左到右递增排列

![avatar](picture/mysql_btree.png)



> B+Tree索引

索引的基本理论



为什么存在建了索引但是查询反而效果差？



mysql锁的分类

+ 对数据类型的操作(读/写)分(主要针对MyISAM)
  + 读锁(共享锁)
  + 写锁(排他锁)

+ 对数据操作的粒度分(主要针对InnoDB,支持事务，采用行锁)
  + 表锁(偏读)
  + 行锁(偏写)

索引失效会导致行锁变成表锁



间隙锁：当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的索引项加锁，对于值在条件范围内但并不存在的记录，叫做“间隙锁”



mysql的架构介绍

索引优化分析

查询截取分析

mysql锁机制

主从复制

