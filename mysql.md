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

+ 非叶子节点不存储data,只存储索引(冗余)，可以放更多的索引
+ 叶子节点包含所有的索引字段
+ 叶子节点用指针相连，提高区间的访问性能

![avatar](picture/mysql_btree_p.png)

## 4. Explain性能分析

### 4.1 概念

​		使用EXPLAIN 关键字可以模拟优化器执行SQL 查询语句，从而知道MySQL 是如何处理你的SQL 语句的。分
析你的查询语句或是表结构的性能瓶颈。

​		用法： Explain+SQL 语句，返回结果如图所示

![avatar](picture/mysql_explain_result.png)

> SQL语句准备

```sql
CREATE TABLE t1(id INT(10) AUTO_INCREMENT,content VARCHAR(100) NULL , PRIMARY KEY (id));
CREATE TABLE t2(id INT(10) AUTO_INCREMENT,content VARCHAR(100) NULL , PRIMARY KEY (id));
CREATE TABLE t3(id INT(10) AUTO_INCREMENT,content VARCHAR(100) NULL , PRIMARY KEY (id));
CREATE TABLE t4(id INT(10) AUTO_INCREMENT,content VARCHAR(100) NULL , PRIMARY KEY (id));

INSERT INTO t1(content) VALUES(CONCAT('t1_',FLOOR(1+RAND()*1000)));
INSERT INTO t2(content) VALUES(CONCAT('t2_',FLOOR(1+RAND()*1000)));
INSERT INTO t3(content) VALUES(CONCAT('t3_',FLOOR(1+RAND()*1000)));
INSERT INTO t4(content) VALUES(CONCAT('t4_',FLOOR(1+RAND()*1000)));
```

### 4.2 Explain返回结果分析

> 1 id

select 查询的序列号,包含一组数字，表示查询中执行select 子句或操作表的顺序。

①id 相同，执行顺序由上至下

```sql
explain select * from t1,t2,t3 where t1.id=t2.id and t2.id=t3.id;
```

![avatar](picture/mysql_explain_id_1.png)

②id 不同，id 不同，如果是子查询，id 的序号会递增，id 值越大优先级越高，越先被执行

```sql
explain select t2.* from t2 where id=(select id from t1 where id=(select t3.id from t3 where t3.content=''));
```

![avatar](picture/mysql_explain_id_2.png)

③有相同也有不同

```sql
# TODO 存在问题
explain select t2.* from t2,(select * from t3 where t3.content='') s3 where s3.id=t2.id;
```

![avatar](picture/mysql_explain_id3.png)

​	id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行衍生= DERIVED



**关注点：id 号每个号码，表示一趟独立的查询。一个sql 的查询趟数越少越好。**

> 2 select_type

select_type 代表查询的类型，主要是用于区别普通查询、联合查询、子查询等的复杂查询。

| select_type 属性     | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| SIMPLE               | 简单的select 查询,查询中不包含子查询或者UNION                |
| PRIMARY              | 查询中若包含任何复杂的子部分，最外层查询则被标记为Primary    |
| DERIVED              | 在FROM 列表中包含的子查询被标记为DERIVED(衍生)<br>MySQL 会递归执行这些子查询, 把结果放在临时表里。 |
| SUBQUERY             | 在SELECT或WHERE列表中包含了子查询                            |
| DEPEDENT SUBQUERY    | 在SELECT或WHERE列表中包含了子查询,子查询基于外层             |
| UNCACHEABLE SUBQUERY | 无法使用缓存的子查询                                         |
| UNION                | 若第二个SELECT出现在UNION之后，则被标记为UNION；<br/>若UNION包含在FROM子句的子查询中,外层SELECT将被标记为：DERIVED |
| UNION RESULT         | 从UNION表获取结果的SELECT                                    |

①SIMPLE

SIMPLE 代表单表查询；

![avatar](picture/mysql_explain_selectType_1.png)

②PRIMARY

查询中若包含任何复杂的子部分，最外层查询则被标记为Primary。

③DERIVED

在FROM 列表中包含的子查询被标记为DERIVED(衍生),MySQL 会递归执行这些子查询, 把结果放在临时表里。

④SUBQUERY

在SELECT 或WHERE 列表中包含了子查询。

![avatar](picture/mysql_explain_selectType_4.png)

⑤DEPENDENT SUBQUERY

在SELECT 或WHERE 列表中包含了子查询,子查询基于外层。

都是where 后面的条件，subquery 是单个值，dependent subquery 是一组值。

⑥UNCACHEABLE SUBQUREY

⑦UNION

若第二个SELECT 出现在UNION 之后，则被标记为UNION；若UNION 包含在FROM 子句的子查询中,外层SELECT
将被标记为：DERIVED。

![avatar](picture/mysql_explain_selectType_7.png)

⑧UNION RESULT

从UNION 表获取结果的SELECT

> 3 table

这个数据是基于哪张表的

> 4 type

type 是查询的访问类型。是较为重要的一个指标，结果值从最好到最坏依次是:

system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL 

一般来说，得保证查询至少达到range 级别，最好能达到ref。

①system

表只有一行记录（等于系统表），这是const 类型的特列，平时不会出现，这个也可以忽略不计

②const

表示通过索引一次就找到了,const 用于比较primary key 或者unique 索引。因为只匹配一行数据，所以很快
如将主键置于where 列表中，MySQL 就能将该查询转换为一个常量。

```sql
explain select * from (select * from t1 where t1.id=1) s;
```



![avatar](picture/mysql_explain_type_1.png)

③eq_ref

唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描。

```sql
explain select * from t1,t2 where t1.id=t2.id;
```

![avatar](picture/mysql_explain_type_2.png)

④ref

​	非唯一性索引扫描，返回匹配某个单独值的所有行.本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体。

在content上没有建立索引之前

![avatar](picture/mysql_explain_type_4_1.png)

在content上没有建立索引之后

```sql
create index idx_content on t2(content);
```

![avatar](picture/mysql_explain_type_4_2.png)

⑤range

只检索给定范围的行,使用一个索引来选择行。key 列显示使用了哪个索引一般就是在你的where 语句中出现了between、<、>、in 等的查询这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。

![avatar](picture/mysql_explain_type_5.png)

![avatar](picture/mysql_explain_type_5_1.png)

⑥index

出现index是sql使用了索引但是没用通过索引进行过滤，一般是使用了覆盖索引或者是利用索引进行了排序分组。

![avatar](picture/mysql_explain_type_6.png)

⑦all

Full Table Scan，将遍历全表以找到匹配的行.

⑧index_merge

在查询过程中需要多个索引组合使用，通常出现在有or 的关键字的sql 中。

⑨ref_or_null

对于某个字段既需要关联条件，也需要null 值得情况下。查询优化器会选择用ref_or_null 连接查询。

![avatar](picture/mysql_explain_type_8.png)

⑩index_subquery

利用索引来关联子查询，不再全表扫描

11unique_subquery

该联接类型类似于index_subquery。子查询中的唯一索引



> 6 possible_key

显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用。

> 7 key

实际使用的索引。如果为NULL，则没有使用索引。

> 8 key_len

表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。key_len 字段能够帮你检查是否充分的利用上了索引。ken_len 越长，说明索引使用的越充分。

如何计算：
①先看索引上字段的类型+长度比如int=4 ; varchar(20) =20 ; char(20) =20
②如果是varchar 或者char 这种字符串字段，视字符集要乘不同的值，比如utf-8 要乘3,GBK 要乘2，
③varchar 这种动态字符串要加2 个字节
④允许为空的字段要加1 个字节
第一组：key_len=age 的字节长度+name 的字节长度=4+1 + ( 20*3+2)=5+62=67
第二组：key_len=age 的字节长度=4+1=5

> 9 ref

显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值。

> 10 rows

rows 列显示MySQL 认为它执行查询时必须检查的行数。越少越好！

> 11 Extra

其他的额外重要的信息

+ Using filesort:说明mysql 会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MySQL 中无法利用索引完成的排序操作称为“文件排序”。优化前后对比：

![avatar](picture/mysql_explain_extra_1.png)

![avatar](picture/mysql_explain_extra_2.png)

+ Using temporary:使了用临时表保存中间结果,MySQL 在对查询结果排序时使用临时表。常见于排序order by 和分组查询group
  by。优化前后对比：

![avatar](picture/mysql_explain_extra_3.png)

+ Using index:Using index 代表表示相应的select 操作中使用了覆盖索引(Covering Index)，避免访问了表的数据行，效率不错！
  如果同时出现using where，表明索引被用来执行索引键值的查找;如果没有同时出现using where，表明索引只是
  用来读取数据而非利用索引执行查找。
  利用索引进行了排序或分组。
+ Using where:表明使用了where 过滤。
+ Using join buffer:使用了连接缓存。
+ impossible where:where 子句的值总是false，不能用来获取任何元组。
+ select tables optimized away:在没有GROUPBY 子句的情况下，基于索引优化MIN/MAX 操作或者对于MyISAM 存储引擎优化COUNT(*)操
  作，不必等到执行阶段再进行计算，查询执行计划生成的阶段即完成优化。

## 5 单表使用索引常见的失效案例

```sql
# 建表语句，通过存储过程在dept插入1_0000条数据，在emp中插入50_0000条数据
CREATE TABLE `dept` (
`id` INT(11) NOT NULL AUTO_INCREMENT,
`deptName` VARCHAR(30) DEFAULT NULL,
`address` VARCHAR(40) DEFAULT NULL,
ceo INT NULL ,
PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
CREATE TABLE `emp` (
`id` INT(11) NOT NULL AUTO_INCREMENT,
`empno` INT NOT NULL ,
`name` VARCHAR(20) DEFAULT NULL,
`age` INT(3) DEFAULT NULL,
`deptId` INT(11) DEFAULT NULL,
PRIMARY KEY (`id`)
#CONSTRAINT `fk_dept_id` FOREIGN KEY (`deptId`) REFERENCES `t_dept` (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

# 查询：show variables like 'log_bin_trust_function_creators';
# 设置：set global log_bin_trust_function_creators=1;
# 当然，如上设置只存在于当前操作，想要永久生效，需要写入到配置文件中：
# 在[mysqld]中加上log_bin_trust_function_creators=1
```

### 5.1 全值匹配我最爱

```sql
EXPLAIN SELECT SQL_NO_CACHE * FROM emp WHERE emp.age=30
EXPLAIN SELECT SQL_NO_CACHE * FROM emp WHERE emp.age=30 and deptid=4
EXPLAIN SELECT SQL_NO_CACHE * FROM emp WHERE emp.age=30 and deptid=4 AND emp.name = 'abcd'
```

![avatar](picture/mysql_example_1.png)

```sql
# 建立索引
CREATE INDEX idx_age_deptid_name ON emp(age,deptid,NAME);
```

![avatar](picture/mysql_example_2.png)

**结论：全值匹配我最爱指的是，查询的字段按照顺序在索引中都可以匹配到！**

注：SQL 中查询字段的顺序，跟使用索引中字段的顺序，没有关系。优化器会在不影响SQL 执行结果的前提下，给你自动地优化。

### 5.2 最佳左前缀法则

![avatar](picture/mysql_example_3.png)

 查询字段与索引字段顺序的不同会导致，索引无法充分使用，甚至索引失效！
原因：使用复合索引，需要遵循最佳左前缀法则，即如果索引了多列，要遵守最左前缀法则。指的是查询从索
引的最左前列开始并且不跳过索引中的列。
**结论：过滤条件要使用索引必须按照索引建立时的顺序，依次满足，一旦跳过某个字段，索引后面的字段都无
法被使用。**

### 5.3 不要在索引列上做任何计算

不在索引列上做任何操作（计算、函数、(自动or 手动)类型转换），会导致索引失效而转向全表扫描。

> 在查询列上做了转换

**字符串不加单引号，则会在name 列上做一次转换！**

```sql
create index idx_name on emp(name);
# name 为varchar类型
explain select sql_no_cache * from emp where name='15';
explain select sql_no_cache * from emp where name=15;
```



![avatar](picture/mysql_explain_example_5_3.png)



### 5.4 索引列上不能有范围查询

```sql
# 使用范围查询后索引的使用效率大大降低，从key_len长度可以看出
explain select sql_no_cache * from emp where emp.age=30 and deptid=5 and emp.name='wjHIvc';
explain select sql_no_cache * from emp where emp.age=30 and deptid<5 and emp.name='wjHIvc';
```



![avatar](picture/mysql_explain_example_5_4.png)

**建议：将可能做范围查询的字段的索引顺序放在最后**

### 5.5 尽量使用覆盖索引

即查询列和索引列一直，不要写select *!

![avatar](picture/mysql_explain_example_5_5_1.png)

### 5.6 使用不等于(!= 或者<>)的时候

mysql 在使用不等于(!= 或者<>)时，有时会无法使用索引会导致全表扫描。

![avatar](picture/mysql_explain_example_5_6.png)



### 5.7 字段的is not null 和is null

![avatar](picture/mysql_explain_example_5_7_1.png)



当字段允许为Null 的条件下：

![avatar](picture/mysql_explain_example_5_7_2.png)

**is not null 用不到索引，is null 可以用到索引。**

### 5.8 like 的前后模糊匹配

```sql
# 在name字段上建立索引
create index idx_name on emp(name);e
```

![avatar](picture/msyql_explain_example_5_8_1.png)

**前缀不能出现模糊匹配！**

### 5.9 减少使用or

![avatar](picture/mysql_explain_example_5_9.png)

使用union all 或者union 来替代：



### 5.X 小结

全值匹配我最爱，最左前缀要遵守；
带头大哥不能死，中间兄弟不能断；
索引列上少计算，范围之后全失效；
LIKE 百分写最右，覆盖索引不写*；
不等空值还有OR，索引影响要注意；
VAR 引号不可丢，SQL 优化有诀窍。

## 6. 关联查询优化

```sql
# 建表语句
CREATE TABLE IF NOT EXISTS `class` (
`id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`card` INT(10) UNSIGNED NOT NULL,
PRIMARY KEY (`id`)
);
CREATE TABLE IF NOT EXISTS `book` (
`bookid` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`card` INT(10) UNSIGNED NOT NULL,
PRIMARY KEY (`bookid`)
);
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO book(card) VALUES(FLOOR(1 + (RAND() * 20)));
```

### 6.1 left join

①没有索引

```sql
explain select sql_no_cache * from class left join book on class.card=book.card;
```



![avatar](picture/mysql_explain_leftJoin_6_1.png)

②如何优化？在哪个表上建立索引？

```sql
create index idx_card on book(card);
explain select sql_no_cache * from class left join book on class.card=book.card;
```

![avatar](picture/mysql_explain_leftJoin_6_1_2.png)

③删除book 表的索引：drop index idx_card on book;

在class 表上建立索引：alter table class add index idx_card(card);

![avatar](picture/mysql_explain_leftJoin_6_1_3.png)

结论：
①在优化关联查询时，只有在被驱动表上建立索引才有效！
②left join 时，左侧的为驱动表，右侧为被驱动表！

### 6.2 inner join

```sql
explain select * from book inner join class on class.card=book.card;
```

![avatar](picture/mysql_explain_query_6_2_1.png)

②两个查询字段调换顺序，发现结果也是一样的

![avatar](picture/mysql_explain_query_6_2_2.png)

③在book 表中，删除9 条记录，再进行inner join

![avatar](picture/mysql_explain_query_6_2_3.png)

④**结论：inner join 时，mysql 会自己帮你把小结果集的表选为驱动表。**

### 6.3 四个关联查询案例分析



<<<<<<< HEAD
## 7 排序分组优化

where条件和on的判断这些过滤条件，作为优先优化的部分，是要被先考虑的！其次，如果有分组和排序，那么也要考虑grouo by 和order by。

### 7.1 无过滤不索引

```sql
# 建立索引
create index idx_age_deptid_name on emp (age,deptid,name);
explain select * from emp where age=40 order by deptid;
# using filesort 说明进行了手工排序！原因在于没有where 作为过滤条件！
explain select * from emp order by age,deptid;
explain select * from emp order by age,deptid limit 10;
```



![avatar](picture/mysql_explain_sort_7_1.png)

**结论： 无过滤，不索引。where，limit 都相当于一种过滤条件，所以才能使用上索引！**

### 7.2 顺序错，必排序

```sql
# 1
explain select * from emp where age=45 order by deptid,name;
# 2
explain select * from emp where age=45 order by deptid,empno;
# 3 empno 字段并没有建立索引，因此也无法用到索引，此字段需要排序！,where 两侧列的顺序可以变换，效果相同，但是order by 列的顺序不能随便变换！
explain select * from emp where age=45 order by name,deptid;
# 4
explain select * from emp where deptid=45 order by age;
```



![avatar](picture/mysql_explain_sort_7_2_1.png)

deptid 作为过滤条件的字段，无法使用索引(age-deptid-name)，因此排序没法用上索引

![avatar](picture/mysql_explain_sort_7_2_2.png)



### 7.3 方向反，必排序

```sql
# 1 如果可以用上索引的字段都使用正序或者逆序，实际上是没有任何影响的，无非将结果集调换顺序。
explain select * from emp where age=45 order by deptid desc, name desc ;
# 2 如果排序的字段，顺序有差异，就需要将差异的部分，进行一次倒置顺序，因此还是需要手动排序的！
explain select * from emp where age=45 order by deptid asc, name desc ;
```

![avatar](picture/mysql_explain_sort_7_3_1.png)



### 7.4 索引的选择

①首先，清除emp 上面的所有索引，只保留主键索引！

```sql
drop index idx_age_deptid_name on emp;
```

②查询：年龄为30 岁的，且员工编号小于101000 的用户，按用户名称排序

```sql
explain SELECT SQL_NO_CACHE * FROM emp WHERE age =30 AND empno <101000 ORDER BY NAME ;
```

③全表扫描肯定是不被允许的，因此我们要考虑优化。

![avatar](picture/mysql_explain_sort_7_4_1.png)

思路：首先需要让where 的过滤条件，用上索引；
查询中，age, empno 是查询的过滤条件，而name 则是排序的字段，因此我们来创建一个此三个字段的复合索引：

```sql
create index idx_age_empno_name on emp(age,empno,name);
```

![avatar](picture/mysql_explain_sort_7_4_2.png)

再次查询，发现using filesort 依然存在。
原因： empno 是范围查询，因此导致了索引失效，所以name 字段无法使用索引排序。
所以，三个字段的符合索引，没有意义，因为empno 和name 字段只能选择其一！

④解决： 鱼与熊掌不可兼得，因此，要么选择empno,要么选择name

```sql
drop index idx_age_empno_name on emp;
create index idx_age_name on emp(age,name);
create index idx_age_empno on emp(age,empno);
```

![avatar](picture/mysql_explain_sort_7_4_3.png)



![avatar](picture/mysql_explain_sort_7_4_4.png)

原因：所有的**排序都是在条件过滤之后才执行的**，所以如果条件过滤了大部分数据的话，几百几千条数据进行排序其实并不是很消耗性能，即使索引优化了排序但实际提升性能很有限。相对的empno<101000 这个条件如果没有用到索引的话，要对几万条的数据进行扫描，这是非常消耗性能的，使用empno 字段的范围查询，过滤性更好（empno 从100000 开始）！



**结论： 当范围条件和group by 或者order by 的字段出现二选一时，优先观察条件字段的过滤数量，如果过滤的数据足够多，而需要排序的数据并不多时，优先把索引放在范围字段上。反之，亦然。**

### 7.5 using filesort

> 双路排序

​		MySQL 4.1 之前是使用双路排序,字面意思就是两次扫描磁盘，最终得到数据，读取行指针和orderby 列，对他们进行排序，然后扫描已经排序好的列表，按照列表中的值重新从列表中读取对应的数据输出。

​		从磁盘取排序字段，在buffer 进行排序，再从磁盘取其他字段。

​		简单来说，取一批数据，要对磁盘进行了两次扫描，众所周知，I/O 是很耗时的，所以在mysql4.1 之后，出现了第二种改进的算法，就是单路排序.

> 单路排序

​		从磁盘读取查询需要的所有列，按照order by 列在buffer 对它们进行排序，然后扫描排序后的列表进行输出，它的效率更快一些，避免了第二次读取数据。并且把随机I/O 变成了顺序IO,但是它会使用更多的空间，因为它把每一行都保存在内存中了。

**单路排序的问题**

​		由于单路是后出的，总体而言好过双路。但是存在以下问题：
​		在sort_buffer 中，方法B 比方法A 要多占用很多空间，因为方法B 是把所有字段都取出, 所以有可能取出的数据的总大小超出了sort_buffer 的容量，导致每次只能取sort_buffer 容量大小的数据，进行排序（创建tmp 文件，多路合并），排完再取取sort_buffer 容量大小，再排……从而多次I/O。
​		结论：本来想省一次I/O 操作，反而导致了大量的I/O 操作，反而得不偿失。



> 如何优化

①增大sort_butter_size 参数的设置

​		不管用哪种算法，提高这个参数都会提高效率，当然，要根据系统的能力去提高，因为这个参数是针对每个进程的1M-8M 之间调整。

②增大max_length_for_sort_data 参数的设置

​		mysql 使用单路排序的前提是排序的字段大小要小于max_length_for_sort_data。提高这个参数，会增加用改进算法的概率。但是如果设的太高，数据总容量超出sort_buffer_size 的概率就增大，明显症状是高的磁盘I/O 活动和低的处理器使用率。（1024-8192 之间调整）

③减少select 后面的查询的字段。

​		当Query 的字段大小总和小于max_length_for_sort_data 而且排序字段不是TEXT|BLOB 类型时，会用改进后的算法——单路排序， 否则用老算法——多路排序。
​		两种算法的数据都有可能超出sort_buffer 的容量，超出之后，会创建tmp 文件进行合并排序，导致多次I/O，但是用单路排序算法的风险会更大一些,所以要提高sort_buffer_size。



### 7.6 覆盖索引

覆盖索引：SQL 只需要通过索引就可以返回查询所需要的数据，而不必通过二级索引查到主键之后再去查询数据。

![avatar](picture/mysql_explain_sort_7_6.png)



### 7.7 group by

group by 使用索引的原则几乎跟order by 一致，唯一区别是group by 即使没有过滤条件用到索引，也可以直接使用索引。

![avatar](picture/mysql_explain_sort_7_7.png)
=======
## 7. 子查询优化

①取所有不为掌门人的员工，按年龄分组！

```sql
select age ,count(*) from t_emp where id not in (select ceo from t_dept where ceo is not null) group by age;
```

![avatar](picture/mysql_explain_subquery_7_1.png)

②如何优化？

+ 解决dept 表的全表扫描，建立ceo 字段的索引：

![avatar](picture/mysql_explain_subquery_7_2.png)

+ 此时，再次查询：

![avatar](picture/mysql_explain_subquery_7_3.png)

+ 进一步优化，替换not in。

```sql
select age ,count(*) from emp e left join dept d on e.id=d.ceo where d.id is null group by age;
```

![avatar](picture/mysql_explain_subquery_7_4.png)

**结论： 在范围判断时，尽量不要使用not in 和not exists，使用left join on xxx is null 代替。**
>>>>>>> adfb928003095ab2339de4ec5b2c0b3d2d47e08f





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





## 8 mysql常见50道面试题

### 8.1 mysql聚簇索引和非聚簇索引的区别是什么

**mysql的索引存储在磁盘, 如果存储在内存中每次断电都要重新生成非常耗时**

​		mysql的索引类型跟存储引擎是相关的，INNODB存储引擎数据文件和索引文件都存储在.ibd文件中，而MYISAM的数据文件存储在.myd文件中，索引存储在.myi文件中。区分聚簇索引和非聚簇索引，只需要判断数据文件是否和索引文件是否存储在一起。

​		INNODB存储引擎在进行数据插入时，数据必须和索引放在一起，建立索引时，如果有主键，则使用主键，没有主键使用唯一键值，没有唯一键，则使用6字节的rowid。为了避免存储数据的冗余，其他索引的叶子节点存储的都是聚簇索引的key值(即地址)。因此，INNODB中既包含聚簇索引，又包含非聚簇索引；而MYISAM中只包含非聚簇索引。

### 8.2 mysql的索引结构有哪些，各自有什么优劣

​		索引的数据结构与具体存储引擎的实现有关，mysql使用较多的索引是Hash索引和B+树索引。其中INNODB引擎的索引为B+树，MEMOERY的引擎为Hash索引。

B+树是一个平衡多叉树，从根节点到每个叶子节点的高度差值不超过1，而且同层级的两个节点之间有指针关联，在B+树上的常规搜索，从根节点到叶子节点的搜索效率基本相当，不会出现大幅度波动，而且基于索引的顺序扫描时，也可以利用双指针快速左右移动，效率非常高。



​		Hash索引就是采用某些哈希算法，将需要存储的键值转换成哈希值，检索时不再跟进B+树那样从根节点逐级遍历，只需要将待查找的键值转换成哈希值，一次即可完成定位，速度非常快。



​		如果每次查询都是等值查找，Hash索引的优势非常大，只需要将键值转换成哈希值，一次定位就可以找到，如果存在Hash碰撞，需要在对应的链表上依次遍历，知道找到待查找项。

​		如果是范围查找，哈希索引的查找效率非常低。原来有序的数据，经过Hash算法后，数据存储在哈希表里会变得无序，范围查找时只有逐个遍历，效率很低。此外，Hash索引也不能利用索引排序，以及like这样的模糊查询。Hash索引也不支持多列联合索引的最左匹配规则。



​		B+树索引的关键字检索效率比较平均，不像B树波动那么大，在有大量值重复的情况下，哈希索引的效率也是极低的，因为存在哈希碰撞问题。



### 8.3 mysql中索引类型有哪些，以及对数据库的性能影响

普通索引：允许被索引的数据列包含重复值

唯一索引：可以保证数据记录的唯一性(允许为null)

主键索引：是一种特殊的唯一索引，一张表中只允许定义一个主键索引，主键用于唯一标识一条记录。

联合索引：索引可以覆盖多个数据列

全文索引：通过建立倒排索引，可以极大提升检索效率，解决字段是否包含问题。



### 8.4 mysql的隔离级别有哪些

​		mysql定义了四种隔离级别，包括一些具体规则，用于限定事务内外哪些改变是可见的，哪些是不可见的。低级别的隔离一般支持更高的并发，并且拥有更高的系统开销。



**Read Uncommitted** 读未提交

在这个隔离级别，所有事务都可以看到未提交事务的执行结果。在这种隔离级别上，会产生很多问题，如脏读



**Read Committed** 读已提交

大多数数据库的默认隔离级别，满足隔离的最早先定义：一个事务开始时，只能看到已提交事务所做的改变，一个事务从开始到提交前，所做的任何数据改变都是不可见的，除非已提交。这种隔离级别也支持“不可重复读”。这就意味着用户运行同一个语句两次，看到的结果时不同的。



**Repeatable Read** 可重复读

mysql数据库的默认隔离级别，它保证了同一个事物的多实例并发读取事物时，会看到同样的结果。但是该隔离级别存在幻读问题，可以通过MVCC解决幻读问题。



**Serializable** 可串行化

​		该级别是最高的隔离级别。他强制事务通过排序，使各个事务之间不会发生相互冲突，从而解决幻读问题。Serializable通过在每个读的数据行上加锁。在这个级别上会出现大量TimeOut和锁竞争Lock Connection现象，实际上很少使用这个隔离级别，但如果用户的应用是为了保证数据的稳定性，需要强制减少并发，也可以选择这种隔离级别。

> 脏读

脏读指一个事务读取了未提交事务执行过程中的数据。

当一个事务A的操作在多次修改数据，而事务还未提交时，另一个并发事务B来读取数据时，读取到事务A未提交的SQL执行结果，这种情况下就会导致数据并非是最终持久化的数据，这个数据就是脏读的数据。



> 不可重复读

不可重复读是指对于数据库中的某个数据，一个事务执行过程中的多次查询查询返回不同的结果，这就是事务在执行过程中，数据被其他事务提交修改了。

不可重复读和脏读的区别就是，脏读是读取了另一个事务未提交的SQL执行结果，而不可重复读是一个事务在执行过程中，另一个事务提交并修改了当前事务在读的数据，导致事务在多次查询的结果不一样。



> 幻读

幻读事务非独立执行时发生的一种现象，如事务T1批量对一个表某一列数据值为1的数据执行修改为2的变更，但是在这时，事务T2对这张表中插入了值为1的记录，并未提交，此时，事务T1刚完成修改操作，发现表中还有一条值为1的记录未修改，而这条值为1的记录是刚刚事务T2插入的，这就是幻读

幻读和不可重复读都是读取到另一个事务已提交的SQL执行结果，所不同的是不可重复读查询的都是同一个数据项，而幻读针对的是一批数据整体。



### 8.5 什么是事务

myql中有三种日志文件

+ bin_log：属于mysql的server层
+ redo_log：归属于INNODB
+ undo_log：归属于INNODB

事务是构成单一逻辑工作单元的操作集合，就是一组SQL的组合，但是这组SQL组合必须是同时成功，如果有一个SQL执行失败，则该事务执行失败。

+ 原子性(Atomicity)：根据定义，原子性是指一个事务是一个不可分割的工作单位，其中的操作要么都做，要么都不做。即要么转账成功，要么转账失败，是不存在中间的状态！操作失败时，通过undo_log实现回滚
+ 隔离性(Isolation)：根据定义，隔离性是指多个事务并发执行的时候，事务内部的操作与其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
+ 持久性(Durability)：根据定义，持久性是指事务一旦提交，它对数据库的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。通过redo_log、bin_log日志共同实现
+ 一致性(Consistency)：根据定义，一致性是指事务执行前后，数据处于一种合法的状态，这种状态是语义上的而不是语法上的。 那什么是合法的数据状态呢？ oK，这个状态是满足预定的约束就叫做合法的状态，再通俗一点，这状态是由你自己来定义的。满足这个状态，数据就是一致的，不满足这个状态，数据就是不一致的！
