# MySQL

 

## 3 基本的Select语句

### 3.1 SQL的分类

+ DDL(数据定义语言, Data Definition Languages): Create, Alter, Drop, Rename, Truncate

+ DML(数据操作语言, Data Manipulation, Language): Insert, Delete, Update, **Select**

+ DCL(数据控制语言, Data Control Language): Commit, Rollback, SavePoint, Grant, Revoke



### 3.2 SQL语言的规则和规范

> 基本规则

+ SQL 可以写在一行或者多行。为了提高可读性，各子句分行写，必要时使用缩进
+ 每条命令以 ; 或 \g 或 \G 结束
+ 关键字不能被缩写也不能分行
+ 关于标点符号
  + 必须保证所有的()、单引号、双引号是成对结束的
  + 必须使用英文状态下的半角输入方式
  + 字符串型和日期时间类型的数据可以使用单引号（' '）表示
  + 列的别名，尽量使用双引号（" "），而且不建议省略as

> 基本规范

+ MySQL 在 Windows 环境下是大小写不敏感的
+ MySQL 在 Linux 环境下是大小写敏感的

①数据库名、表名、表的别名、变量名是严格区分大小写的

②关键字、函数名、列名(或字段名)、列的别名(字段的别名) 是忽略大小写的。

+ 推荐采用统一的书写规范：

①数据库名、表名、表别名、字段名、字段别名等都小写

②SQL 关键字、函数名、绑定变量等都大写



```sql
# 通过sql文件导出数据
source E:\myfile\studydata\Java\mysql\atguigudb.sql
```

### 3.3 SELECT

#### 3.3.1 基本查询

> 列的别名

```sql
# as(alias, 别名), 可以省略
# 列的别名可以使用一对""引起来。不要使用''
SELECT employee_id emp_id, last_name, department_id as "部门id"
FROM employees
```



> 去重

```sql
# 查询员工表中一共有哪些部门id
SELECT DISTINCT department_id FROM employees;
```



> 空值参与运算

**空值参与运算: 结果一定也为空**

空值: null，null不等同于0，'', 'null'

```sql
select employee_id, salary annul_month, salary * (1 + commission_pct) * 12 annul_year
from employees;
# 这里commission_pct有为空的情况, 所以当commission_pct为null, 其运算的结果也为null
select employee_id, salary annul_month, salary * (1 + IFNULL(commission_pct, 0)) * 12 annul_year
from employees;
# IFNULL:如果当前值为null，将该值替换为0
```



> 着重号 ``

```sql
# 着重号 ``, 当表名和数据库关键字重复了, 可以使用``区分
SELECT * FROM `order`;
```



> 为表增加一个公共的单独列

```sql
# 查询常数
SELECT 'iflytek',employee_id, last_name
FROM employees;
```



> 显示表结构

```sql
# 显示表结构
DESCRIBE employees;
```

#### 3.3.2 Where 过滤条件

```sql
# 查询90号部门的员工信息
SELECT * FROM employees
WHERE department_id=90;
```



## 4 运算符

### 4.1 算术运算符

**+, -, *, /(DIV), %(mod)**

```sql
# dual是一个伪表
select 100, 100+0, 100-0, 100+50, 100+30.5
from dual;
# sql语言中没有字符串, 这里的'1'在运算过程中仍然作为数字1
select 100 + '1' from dual;

SELECT 100,100*1,100*1.0,100/1.0,100 / 2, 100 + 2 * 5, 2 * 5 / 2, 100 / 3, 100 DIV 0
FROM DUAL;

# 查询员工id为偶数的员工信息
SELECT employee_id, last_name, salary
FROM employees
WHERE employee_id % 2 = 0;
```

### 4.2 比较运算符

比较运算符用来对表达式左边的操作数和右边的操作数进行比较，比较的结果为**真则返回1**，比较的结果
为**假则返回0**，其他情况则返回NULL。

```sql
# 如果数值与字符串比较大小, 字符串存在隐式转换, 如果转换不成功,则看做0
SELECT 1=2, 1!=2, 1='1',1='a',0='a' FROM DUAL;

# 两边都是字符串的话，则按照ANSI的比较规则进行比较
SELECT 'a'='b', 'ab'='ab', 'a'='b' FROM DUAL; 

# 只要有null参与运算, 结果均为null
select 1=null from dual;
select null=null from dual;

# <=>: 安全等于, 在没有null参与的运算中与=完全一致，在有null参与的情况下，根据情况判断不会返回null
SELECT 1 <=> 2, 1<=>'1', NULL <=>NULL
FROM DUAL;
```



```sql
# ① IS NULL \ IS NOT NULL \ ISNULL:是一个函数
# 查询员工表中commission_pct为null的员工信息
SELECT employee_id, last_name, salary
FROM employees
WHERE commission_pct IS NULL;

SELECT employee_id, last_name, salary
FROM employees
WHERE  ISNULL(commission_pct);

# 查询员工表中commission_pct为不为null的员工信息
SELECT employee_id, last_name, salary
FROM employees
WHERE commission_pct IS NOT NULL;

# ② LEAST(), GREATEST
SELECT LEAST('a', 'g', 't', 'm'), GREATEST('a', 'g', 't', 'm')
FROM DUAL;

# between 条件1 and 条件2(注：包含条件1和条件2的边界)

# in (set) \ not in (set)
# 查询部门号为10,20,30的员工信息
select last_name, salary, department_id
from employees
where department_id in (10,20,30);

# 查询工资不是6000, 7000, 8000
SELECT last_name, salary, department_id
FROM employees
WHERE salary NOT IN(6000, 7000, 8000);

# 查询last_name中包含字符'a'且包含字符'e'的员工信息
SELECT last_name FROM employees
WHERE last_name LIKE '%a%' AND last_name LIKE '%e%';

# 查询last_name中第二个字符为'a'的员工信息
# _代表一个不确定字符
SELECT last_name FROM employees
WHERE last_name LIKE '_a%';

# 查询last_name中第二个字符'_'第三个字符为'a'的员工信息
# \是一个转义字符
SELECT last_name FROM employees
WHERE last_name LIKE '_\_a%';
```

### 4.3 逻辑运算符

```sql
# OR  || AND && NOT ! XOR(异或)
#查询基本薪资不在9000-12000之间的员工编号和基本薪资
SELECT employee_id,salary FROM employees
WHERE NOT (salary >= 9000 AND salary <= 12000);
```

### 4.4 位运算符

```sql
# 位运算符
SELECT 12 & 5, 12 | 5,12 ^ 5 FROM DUAL;
```

## 5 排序和分页

### 5.1 排序数据

```sql
# 练习：按照salary从高到低排列
# ASC(ascend) 升序
# DESC(descend) 降序
SELECT employee_id, last_name, salary
FROM employees
ORDER BY salary DESC;

# 我们可以使用列的别名进行排序
# 列的别名只能在order by中使用, 不能在where中使用
# 主要由于sql语句的执行顺序, from -> where -> select -> order by 在where的时候是看不到select起的别名
SELECT employee_id, salary, salary * 12 annual_sal
FROM employees
ORDER BY annual_sal;
```



### 5.2 分页

```sql
# 分页
# limit 0,5 ,0表示偏移量, 5表示显示页的大小
# (current - 1) * pageSize, pageSize
SELECT employee_id, last_name
FROM employees
LIMIT 5, 5;

# where ... order by ... limit
SELECT employee_id, last_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 0, 10;
# limit: 严格说是limit 位置偏移量, 条目数
# 结构， limit 0, 条目数 等同于 limit 条目数
```

## 6 多表查询

```sql
## 6 多表查询
# 出现笛卡尔积错误, 单纯的对两个表进行查询
# 出现的原因是: 缺少了连接条件
SELECT e.employee_id, d.department_name
FROM employees e, departments d
WHERE e.department_id=d.department_id;

# 查询员工的employee_id, last_name, department_name, city
SELECT employee_id, last_name, department_name, city
FROM employees e, departments d, locations l
WHERE e.department_id=d.department_id AND d.location_id=l.location_id;


```

#### 6.1 等值连接 VS  非等值连接

```sql
/*
多表查询

角度1：等值连接 VS  非等值连接

角度2：自连接  VS  非自连接

角度3：内连接  VS  外连接
*/

# 角度1：等值连接 VS  非等值连接
# 非等值连接

SELECT e.last_name, e.salary, j.grade_level
FROM employees e, job_grades j
WHERE e.salary BETWEEN j.lowest_sal AND j.highest_sal;
```

#### 6.2 自连接  VS  非自连接

```sql
# 角度2：自连接  VS  非自连接
# 练习：查询员工id，员工姓名，及其管理者的id和姓名
SELECT e1.employee_id, e1.last_name, e2.employee_id, e2.last_name
FROM employees e1, employees e2
WHERE e1.employee_id = e2.manager_id;
```



#### 6.3 内连接  VS  外连接

+ 内连接: 合并具有同一列的两个以上的表的行, 结果集中不包含一个表与另一个表不匹配的行
+ 外连接: 两个表在连接过程中除了返回满足连接条件的行以外还返回左（或右）表中不满足条件的
  行 ，这种连接称为左（或右） 外连接。没有匹配的行时, 结果表中相应的列为空(NULL)。其中外连接还包括左外连接、右外连接和全连接
+ 如果是左外连接，则连接条件中左边的表也称为`主表`，右边的表称为`从表`。
+ 如果是右外连接，则连接条件中右边的表也称为`主表`，左边的表称为`从表`。

```sql
# SQL99语言的外连接join ... on
# 左外连接
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id;

# 右外连接, 这里可以查出一些没有员工的部门
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id;

# 全连接: 但是MySQL不支持这样的全连接
SELECT employee_id, department_name
FROM employees e FULL JOIN departments d
ON e.department_id=d.department_id;
```



#### 6.4 7种Join的实现

![avatar](picture/sql_join.png)

> 中图：内连接

```sql
# 中图：内连接
SELECT e.employee_id,d.department_name
FROM employees e INNER JOIN departments d
ON e.department_id=d.department_id;
```

> 左上图：左外连接

```sql
# 左上图：左外连接
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id;
```

> 右上图：右外连接

```sql
# 右上图：右外连接
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id;
```

> 左中图

```sql
# 左中图
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
WHERE d.department_id IS NULL;
```

> 右中图

```sql
# 右中图
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id
WHERE e.department_id IS NULL;
```

> 左下图

```sql
# 左下图：满外连接
# 方式1：左上图 union all 右中图
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
UNION ALL
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id
WHERE e.department_id IS NULL;

# 方式2：左中图 union all 右上图
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
WHERE d.department_id IS NULL
UNION ALL
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id
```

> 右下图

```sql
# 右下图
# 左中图 union all 右中图
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
WHERE d.department_id IS NULL
UNION ALL
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id
WHERE e.department_id IS NULL;
```

#### 6.5 SQL99语法新特性-自然连接

SQL99 在 SQL92 的基础上提供了一些特殊语法，比如 NATURAL JOIN 用来表示自然连接。我们可以把
自然连接理解为 SQL92 中的等值连接。它会帮你自动查询两张连接表中所有相同的字段，然后进行等值
连接。

```sql
# SQL99语法新特性-自然连接
# natural join
SELECT employee_id, last_name, department_name
FROM employees e NATURAL JOIN departments d;

# using
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
USING (department_id);
```

#### 6.6 练习

```sql
# 1.显示所有员工的姓名，部门号和部门名称。
SELECT e.last_name, e.department_id, d.department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id;

# 2.查询90号部门员工的job_id和90号部门的location_id
SELECT e.last_name, e.job_id,e.department_id, d.location_id
FROM employees e INNER JOIN departments d
ON e.department_id=d.department_id
WHERE e.department_id=90;

# 3.选择所有有奖金的员工的 last_name , department_name , location_id , city
SELECT e.last_name, d.department_name, e.commission_pct,d.location_id, l.city
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
LEFT JOIN locations l
ON d.location_id=l.location_id
WHERE e.commission_pct IS NOT NULL;


# 4.选择city在Toronto工作的员工的 last_name , job_id , department_id , department_name
SELECT e.last_name, e.job_id, e.department_id,d.department_name, l.city
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
LEFT JOIN locations l
ON d.location_id=l.location_id
WHERE l.city='Toronto';

# 5.查询员工所在的部门名称、部门地址、姓名、工作、工资，其中员工所在部门的部门名称为’Executive’
SELECT e.last_name, e.job_id, e.salary,d.department_name, l.street_address 
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
LEFT JOIN locations l
ON d.location_id=l.location_id
WHERE d.department_name='Executive';
# 6.选择指定员工的姓名，员工号，以及他的管理者的姓名和员工号，结果类似于下面的格式
# employees Emp# manager Mgr#
SELECT e1.last_name, e1.employee_id, e2.last_name manager, e2.employee_id manager_id
FROM employees e1 LEFT JOIN employees e2
ON e1.manager_id=e2.employee_id; 
# kochhar 101 king 100
# 7.查询哪些部门没有员工
SELECT e.employee_id, e.last_name, d.department_name
FROM employees e RIGHT JOIN departments d
ON e.department_id=d.department_id
WHERE e.department_id IS NULL;
# 8. 查询哪个城市没有部门
SELECT d.department_id, d.department_name, l.city
FROM departments d RIGHT JOIN locations l
ON d.location_id=l.location_id
WHERE d.department_id IS NULL;
# 9. 查询部门名为 Sales 或 IT 的员工信息
SELECT e.employee_id, e.last_name, d.department_name
FROM employees e LEFT JOIN departments d
ON e.department_id=d.department_id
# where d.department_name='Sales' or d.department_name='IT';
WHERE d.department_name IN ('Sales', 'IT');
```

## 7 单行函数

### 7.1 基本函数

```sql
# SIGN(X)：返回X的符号。正数返回1，负数返回-1，0返回0
# CEIL(x)，CEILING(x)：返回大于或等于某个值的最小整数		
# RAND(x)：返回0~1的随机值，其中x的值用作种子值，相同的X值会产生相同的随机数
# SQRT(x)：返回x的平方根。当X的值为负数时，返回NULL
SELECT
ABS(-123),ABS(32),SIGN(-23),SIGN(43),PI(),CEIL(32.32),CEILING(-43.23),FLOOR(32.32),
FLOOR(-43.23),MOD(12,5)
FROM DUAL;
```

### 7.2 三角函数

```sql
# RADIANS(x):将角度转化为弧度，其中，参数x为角度值
# DEGREES(x):将弧度转化为角度，其中，参数x为弧度值
SELECT RADIANS(30),RADIANS(60),RADIANS(90),DEGREES(2*PI()),DEGREES(RADIANS(90))
FROM DUAL;
```

### 7.3 进制转换

```sql
# BIN(x):返回x的二进制编码
# HEX(x):返回x的十六进制编码
# OCT(x):返回x的八进制编码
# CONV(x,f1,f2):返回f1进制数变成f2进制数
SELECT BIN(10),HEX(10),OCT(10),CONV(10,2,8) FROM DUAL;
```

### 7.4 字符串函数

```sql
# CHAR_LENGTH(s): 返回字符串s的字符数。作用与CHARACTER_LENGTH(s)相同
# LENGTH(s):返回字符串s的字节数，和字符集有关
# CONCAT(s1,s2,......,sn):连接s1,s2,......,sn为一个字符串
# CONCAT_WS(x,s1,s2,......,sn):同CONCAT(s1,s2,...)函数，但是每个字符串之间要加上x
# LTRIM(s):去掉字符串s左侧的空格
# TRIM(s):去掉字符串s开始与结尾的空格
# REVERSE(s):返回s反转后的字符串
# NULLIF(value1,value2):比较两个字符串，如果value1与value2相等，则返回NULL，否则返回value1
```

> **注意：MySQL中，字符串的位置是从1开始的。**

```sql
# LPAD:右对齐
# RPAD:左对齐
SELECT employee_id, last_name, LPAD(salary, 10, ' ')
FROM employees;
# CONCAT(s1,s2,......,sn):连接s1,s2,......,sn为一个字符串
select concat('%', last_name, '%') from employees;
```

### 7.5 日期和时间函数

```sql
# NOW(), SYSDATE(): 返回当前系统日期和时间
SELECT CURDATE(), CURRENT_DATE(),CURTIME(),NOW(), SYSDATE() FROM DUAL;

# 日期与时间戳
SELECT UNIX_TIMESTAMP(), FROM_UNIXTIME(1651491896)
FROM DUAL;

# 获取月份，星期，星期数，天数，
SELECT YEAR(CURDATE()),MONTH(CURDATE()),DAY(CURDATE()),
HOUR(CURTIME()),MINUTE(NOW()),SECOND(SYSDATE())
FROM DUAL;

# 获取某一天是一年中的第几天，第几个季度，等等
SELECT MONTHNAME('2021-10-26'),DAYNAME('2021-10-26'),WEEKDAY('2021-10-26'),
QUARTER(CURDATE()),WEEK(CURDATE()),DAYOFYEAR(NOW()),
DAYOFMONTH(NOW()),DAYOFWEEK(NOW())
FROM DUAL;

# 日期操作函数
# 获取距离当前时间有多少秒
SELECT EXTRACT(SECOND FROM NOW()) FROM DUAL;
```

### 7.7 计算日期和时间的函数

```sql
# 在当前的年上加上一年
SELECT NOW(), DATE_ADD(NOW(), INTERVAL 1 YEAR) FROM DUAL;

SELECT
# 当前时间与某个时间之间的差多少天
DATEDIFF(NOW(),'2021-10-01'),
TIMEDIFF(NOW(),'2021-10-25 22:10:10'),FROM_DAYS(366),TO_DAYS('0000-12-25'),
LAST_DAY(NOW()),MAKEDATE(YEAR(NOW()),12),MAKETIME(10,21,23),PERIOD_ADD(20200101010101,10)
FROM DUAL;
```

### 7.8 日期的格式化与解析

```sql
# 按照某种格式输出日期
SELECT DATE_FORMAT(CURDATE(),'%Y-%m-%d') FROM DUAL;

# 日期的格式化
SELECT CONCAT(DATE_FORMAT(CURDATE(),'%Y-%m-%d'),' ', TIME_FORMAT(NOW(), '%h:%i:%S'))
FROM DUAL;
```

## 8 聚合函数

### 8.1 常见的几个聚合函数

> AVG() / SUM()

```sql
# AVG() / SUM():只适用于数值类型的
# SUM()计算过程中过滤掉空值，不计算空值
# AVG() = SUM() / COUNT(1)
SELECT AVG(salary), SUM(salary)
FROM employees;
```

>  MAX()  / MIN()

```sql
# MAX() / MIN():适用于数值类型、字符串类型和日期类型
SELECT MAX(salary), MIN(salary)
FROM employees;
# 字符串类型
SELECT MAX(last_name), MIN(last_name)
FROM employees;
# 日期类型
SELECT MAX(hire_date), MIN(hire_date)
FROM employees;
```

> COUNT()

**COUNT(*)返回表中记录总数，适用于任意数据类型**

```sql
SELECT COUNT(employee_id) FROM employees;
select count(1) from employees;
# count(commission_pct)不能统计字段中包含null的条目
```



### 8.2 GROUP BY的使用

#### 8.2.1 单列分组

```sql
# GROUP BY
# 需求：查询各个部门的平均工资、最高工资
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id;

# 查询各个job_id的平均工资
SELECT job_id, AVG(salary)
FROM employees
GROUP BY job_id;
```

#### 8.2.2 多列分组

```sql
# 查询各个department_id中不同job_id的平均工资
SELECT department_id, job_id,AVG(salary)
FROM employees
GROUP BY department_id, job_id;

## 结论：SELECT中出现的非组函数的字段必须声明在GROUP BY中
## 反之，GROUP BY中声明的字段可以不出现在SELECT中

## GROUP BY 声明在FROM后面，WHERE的前面，ORDER BY前面，LIMIT的前面
```

### 8.3 HAVING的使用

```sql
# HAVING的使用
# 查询各个部门中最高工资比10000高的部门信息
SELECT department_id, MAX(salary)
FROM employees
GROUP BY department_id;

# 结论1：如果过滤条件中使用了聚合函数，则必须使用HAVING来替换WHERE。否则报错
# 结论2：HAVING的声明必须在GROUP BY的后面
SELECT department_id, MAX(salary)
FROM employees
GROUP BY department_id
HAVING MAX(salary) > 10000;

# 在开发中，使用HAVING的前提是SQL中使用了GROUP BY

/*
WHERE和HAVING的对比
1.从使用范围上，HAVING的适用范围更广
2.如果过滤条件中没有聚合函数：这种情况下，WHERE的执行效率要高于HAVING
*/

/*SQL99语法
SELECT ...,...,...(存在聚合函数)
FROM ...,...,... JOIN ... ... ON(多表连接的条件)
WHERE 多表的连接条件 AND 不包含聚合函数的过滤条件
GROUP BY ...,...
HAVING 包含聚合函数的过滤条件
ORDER BY ...,...
LIMIT ...,...
*/
```

### 8.4 SQL语句的执行顺序

```sql
/*
SQL的执行顺序
-> FROM ...,... -> ON -> (LEFT / RIGHT JOIN) 
-> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT
-> ORDER BY -> LIMIT
*/
```



## 9 子查询

### 9.1 单行子查询

```sql
# 子查询
# 需求：谁的工资比Abel的高？
# 自连接
SELECT e2.last_name, e2.salary
FROM employees e1, employees e2
WHERE e1.last_name='Abel' AND e2.salary > e1.salary;

# 子查询
SELECT employee_id, salary
FROM employees
WHERE salary > (SELECT salary FROM employees WHERE last_name='Abel');

# 称谓：外查询(主查询)、内查询(子查询)


/*
子查询分类
角度1(返回结果的条目数)：单行子查询  VS  多行子查询
角度2(内查询是否被执行多次)：相关子查询  VS  不相关子查询
比如：相关子查询：查询工资大于本部门平均工资的员工信息
      不相关子查询：查询工资大于本公司平均工资的员工信息
*/
# 题目：查询工资大于149号员工工资的员工的信息
SELECT employee_id, salary
FROM employees
WHERE salary > (SELECT salary FROM employees WHERE employee_id=149);
# 子查询的编写技巧：从里往外写，从外往里写
```

### 9.2 多行子查询

```sql
# 多行子查询的操作符
# IN ANY ALL SOME(同ANY)

# 题目：返回其它job_id中比job_id为‘IT_PROG’部门任一工资低的员工的员工号、姓名、job_id 以及salary
SELECT employee_id,last_name,job_id, salary
FROM employees
WHERE salary < ANY (SELECT salary FROM employees WHERE job_id='IT_PROG')
AND job_id <> 'IT_PROG';

# 题目：查询平均工资最低的部门id
# MySQL中聚合函数不能嵌套使用
# 方式1
SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary) = (
	SELECT MIN(avg_sal)
	FROM 
	(SELECT department_id, AVG(salary) avg_sal
	FROM employees
	GROUP BY department_id) t
);
# 方式2
SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary) <= ALL (
	SELECT AVG(salary) avg_sal
	FROM employees
	GROUP BY department_id
);

# 题目：查询员工中工资大于本部门平均工资的员工的last_name,salary和其department_id
# 方式1：使用相关子查询
SELECT last_name,salary,department_id
FROM employees e1
WHERE salary > (
	SELECT AVG(salary)
	FROM employees e2
	WHERE e2.department_id=e1.department_id
);

# 方式2：在FROM中声明子查询
SELECT e.last_name,e.salary,e.department_id
FROM employees e, (
	SELECT department_id, AVG(salary) avg_sal
	FROM employees
	GROUP BY department_id
) tmp
WHERE tmp.department_id=e.department_id
AND e.salary > tmp.avg_sal;
```

### 9.3 EXISTS与NOT EXISTS

```sql
# 题目：查询公司管理者的employee_id，last_name，job_id，department_id信息
# 方式1
SELECT employee_id, last_name, job_id, department_id
FROM employees
WHERE employee_id IN (
	SELECT manager_id FROM employees
);
# 方式2
SELECT employee_id, last_name, job_id, department_id
FROM employees e1
WHERE EXISTS (
	SELECT *
	FROM employees e2
	WHERE e1.employee_id=e2.manager_id
);

# 题目：查询departments表中，不存在于employees表中的部门的department_id和department_name
# 方式1:左连接
SELECT e.department_id, d.department_name
FROM departments d LEFT JOIN employees e
ON d.department_id=e.department_id
WHERE e.department_id IS NULL;
# 方式2
SELECT department_id, department_name
FROM departments d
WHERE NOT EXISTS (
	SELECT * 
	FROM employees e
	WHERE d.department_id=e.department_id
);
```



## 10 创建和管理表

> 创建数据库

```sql
# 创建数据库并制定字符集, 如果没有制定字符集将采用数据库默认的字符集
CREATE DATABASE IF NOT EXISTS test01_office CHARACTER SET 'utf8';
# 查看创建数据库的信息
show create database test01_office

# 删除库
drop database if exists test01
```



> 创建表

```sql
CREATE TABLE IF NOT EXISTS `ucenter_member` (
  `id` INT PRIMARY KEY AUTO_INCREMENT COMMENT '用户id',
  `openid` VARCHAR(128) DEFAULT NULL COMMENT '微信openid',
  `mobile` VARCHAR(11) DEFAULT '' COMMENT '手机号',
  `password` VARCHAR(255) DEFAULT NULL COMMENT '密码',
  `nickname` VARCHAR(50) DEFAULT NULL COMMENT '昵称',
  `sex` TINYINT(2) UNSIGNED DEFAULT NULL COMMENT '性别 1 女，2 男',
  `age` TINYINT(3) UNSIGNED DEFAULT NULL COMMENT '年龄',
  `avatar` VARCHAR(255) DEFAULT NULL COMMENT '用户头像',
  `sign` VARCHAR(100) DEFAULT NULL COMMENT '用户签名',
  `is_disabled` TINYINT(1) NOT NULL DEFAULT '0' COMMENT '是否禁用 1（true）已禁用，  0（false）未禁用',
  `is_deleted` TINYINT(1) NOT NULL DEFAULT '0' COMMENT '逻辑删除 1（true）已删除， 0（false）未删除',
  `gmt_create` DATETIME NOT NULL COMMENT '创建时间',
  `gmt_modified` DATETIME NOT NULL COMMENT '更新时间'
) ENGINE=INNODB DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```



**复制现有表到新的表中**

```sql
#3. 将表departments中的数据插入新表dept02中
# 这里使用departments的所有字段建表dept02, 同时也会导入departments的对应字段数据
# 可以根据select查询结果创建表，同时查询结果的别名，可以创建新表的列名
CREATE TABLE dept02
AS
SELECT *
FROM atguigudb.departments;
```



**修改或者增加表的结构**

```sql
#5. 将列last_name的长度增加到50
DESC emp01;
ALTER TABLE emp01
MODIFY last_name VARCHAR(50);
# http://www.google.com.hk/
#7. 删除表emp01
DROP TABLE IF EXISTS emp01;

#9. 在表dept02和emp01中添加新列test_column，并检查所作的操作
ALTER TABLE emp01 ADD test_column VARCHAR(10);
# 添加字段phone_num在emp_name字段后面
alter table emp01 add phone_num varchar(20) after emp_name;

#10.直接删除表emp01中的列 department_id
ALTER TABLE emp01
DROP COLUMN department_id;

# 查看创建表的信息
show create table employees;

# 重命名表
RENAME TABLE myemp1 to emp1;
```

**删除表**

```sql
# 删除表
DROP TABLE IF EXISTS emp01;

# 清空表，只是清除表中的数据，表的结构仍然保存
TRUNCATE TABLE employees;

# 对比TRUNCATE table和DELETE from table
# 相同点：都可以实现清空表中所有数据，同时保留表的结构
# 不同点: TRUNCATE 一旦执行该操作后，表中数据清除完，但是数据不可以回滚。
#		 DELETE 一旦执行后 表中数据可以全部清除(不带WHERE) 同时数据可以实现回滚
```



**DDL和DML的说明**

+ DDL的操作一旦执行，就不可以回滚。由于DDL语句执行完语句后一定会执行一次COMMIT，此操作不受当前设置SET AUTOCOMMIT=FALSE的影响
+ DML的操作默认情况，一旦执行，也可以不回滚，但是如果在执行DML之前，执行了set autocommit=false，则执行的DML操作就可以实现回滚



> 提交和回滚

```sql
# COMMIT：一旦提交数据后，即执行COMMIT，数据被永久的保存在数据库中，意味着数据不可回滚

# ROLLBACK:一旦执行ROLLBACK，则可以实现数据的回滚。回滚到最近的一次COMMIT操作之后。
```



## 11 数据类型

**MySQL中的数据类型**

| 类型             | 类型举例                                                     |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | TINYINT、SMALLINT、MEDIUMINT、INT(或INTEGER)、BIGINT         |
| 浮点类型         | FLOAT、DOUBLE                                                |
| 定点数类型       | DECIMAL                                                      |
| 位类型           | BIT                                                          |
| 日期时间类型     | YEAR、TIME、DATE、DATETIME、TIMESTAMP                        |
| 文本字符串类型   | CHAR、VARCHAR、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT          |
| 枚举类型         | ENUM                                                         |
| 集合类型         | SET                                                          |
| 二进制字符串类型 | BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB      |
| JSON类型         | JSON对象、JSON数组                                           |
| 空间数据类型     | 单值类型：GEOMETRY、POINT、LINESTRING、POLYGON；<br/>集合类型：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、<br/>GEOMETRYCOLLECTION |

**常见数据类型的属性，如下：**

| MySQL关键字        | 含义                     |
| ------------------ | ------------------------ |
| NULL               | 数据列可包含NULL值       |
| NOT NULL           | 数据列不允许包含NULL值   |
| DEFAULT            | 默认值                   |
| PRIMARY KEY        | 主键                     |
| AUTO_INCREMENT     | 自动递增，适用于整数类型 |
| UNSIGNED           | 无符号                   |
| CHARACTER SET name | 指定一个字符集           |

### 11.1 整数类型

整数类型一共有 5 种，包括 TINYINT、SMALLINT、MEDIUMINT、INT（INTEGER）和 BIGINT。
它们的区别如下表所示：

| 整数类型     | 字节 | 有符号数取值范围                         | 无符号数取值范围       |
| ------------ | ---- | ---------------------------------------- | ---------------------- |
| TINYINT      | 1    | -128~127                                 | 0~255                  |
| SMALLINT     | 2    | -32768~32767                             | 0~65535                |
| MEDIUMINT    | 3    | -8388608~8388607                         | 0~16777215             |
| INT、INTEGER | 4    | -2147483648~2147483647                   | 0~4294967295           |
| BIGINT       | 8    | -9223372036854775808~9223372036854775807 | 0~18446744073709551615 |

#### 11.1.1 可选属性

整数类型的可选属性有三个：

> M

`M` : 表示显示宽度，M的取值范围是(0, 255)。例如，int(5)：当数据宽度小于5位的时候在数字前面需要用
字符填满宽度。该项功能需要配合“ ZEROFILL ”使用，表示用“0”填满宽度，否则指定显示宽度无效。
如果设置了显示宽度，那么插入的数据宽度超过显示宽度限制，会不会截断或插入失败？
答案：不会对插入的数据有任何影响，还是按照类型的实际宽度进行保存，即显示宽度与类型可以存储的
值范围无关。从MySQL 8.0.17开始，整数数据类型不推荐使用显示宽度属性。
整型数据类型可以在定义表结构时指定所需要的显示宽度，如果不指定，则系统为每一种类型指定默认
的宽度值。
举例：

```sql
CREATE TABLE test_int1 ( x TINYINT,　y SMALLINT,　z MEDIUMINT,　m INT,　n BIGINT );
```

> UNSIGNED

`UNSIGNED` : 无符号类型（非负），所有的整数类型都有一个可选的属性UNSIGNED（无符号属性），无
符号整数类型的最小取值为0。所以，如果需要在MySQL数据库中保存非负整数值时，可以将整数类型设
置为无符号类型。
int类型默认显示宽度为int(11)，无符号int类型默认显示宽度为int(10)。

> ZEROFILL

`ZEROFILL `: 0填充,（如果某列是ZEROFILL，那么MySQL会自动为当前列添加UNSIGNED属性），如果指
定了ZEROFILL只是表示不够M位时，用0在左边填充，如果超过M位，只要不超过数据存储范围即可。
原来，在 int(M) 中，M 的值跟 int(M) 所占多少存储空间并无任何关系。 int(3)、int(4)、int(8) 在磁盘上都
是占用 4 bytes 的存储空间。也就是说，int(M)，必须和UNSIGNED ZEROFILL一起使用才有意义。如果整
数值超过M位，就按照实际位数存储。只是无须再用字符 0 进行填充。



`TINYINT `：一般用于枚举数据，比如系统设定取值范围很小且固定的场景。
`SMALLINT` ：可以用于较小范围的统计数据，比如统计工厂的固定资产库存数量等。
`MEDIUMINT` ：用于较大整数的计算，比如车站每日的客流量等。
`INT、INTEGER `：取值范围足够大，一般情况下不用考虑超限问题，用得最多。比如商品编号。
`BIGINT` ：只有当你处理特别巨大的整数时才会用到。比如双十一的交易量、大型门户网站点击量、证
券公司衍生产品持仓等。



### 11.2 浮点类型

浮点数和定点数类型的特点是可以处理小数，你可以把整数看成小数的一个特例。因此，浮点数和定点
数的使用场景，比整数大多了。 MySQL支持的浮点数类型，分别是 FLOAT、DOUBLE、REAL。

+ FLOAT 表示单精度浮点数；
+ DOUBLE 表示双精度浮点数



### 11.3 定点数类型

+ MySQL中的定点数类型只有 DECIMAL 一种类型

| 数据类型                 | 字节数  | 含义               |
| ------------------------ | ------- | ------------------ |
| DECIMAL(M,D),DEC,NUMERIC | M+2字节 | 有效范围由M和D决定 |

+ 使用 DECIMAL(M,D) 的方式表示高精度小数。其中，M被称为精度，D被称为标度。0<=M<=65，
  0<=D<=30，D<M。例如，定义DECIMAL（5,2）的类型，表示该列取值范围是-999.99~999.99。
+ DECIMAL(M,D)的最大取值范围与DOUBLE类型一样，但是有效的数据范围是由M和D决定的。
  DECIMAL 的存储空间并不是固定的，由精度值M决定，总共占用的存储空间为M+2个字节。也就是
  说，在一些对精度要求不高的场景下，比起占用同样字节长度的定点数，浮点数表达的数值范围可
  以更大一些。
+ 定点数在MySQL内部是以`字符串`的形式进行存储，这就决定了它一定是精准的。
+ 当DECIMAL类型不指定精度和标度时，其**默认为DECIMAL(10,0)。**当数据的精度超出了定点数类型的
  精度范围时，则MySQL同样会进行四舍五入处理。
+ 浮点数 vs 定点数

①浮点数相对于定点数的优点是在长度一定的情况下，浮点类型取值范围大，但是不精准，适用
于需要取值范围大，又可以容忍微小误差的科学计算场景（比如计算化学、分子建模、流体动
力学等）

②定点数类型取值范围相对小，但是精准，没有误差，适合于对精度要求极高的场景 （比如涉
及金额计算的场景）



```sql
# 数据库中二进制和十进制转换可以
select f1 + 0 from test_bit1;
```



### 11.4 日期与时间类型

日期与时间是重要的信息，在我们的系统中，几乎所有的数据表都用得到。原因是客户需要知道数据的
时间标签，从而进行数据查询、统计和处理。
MySQL有多种表示日期和时间的数据类型，不同的版本可能有所差异，MySQL8.0版本支持的日期和时间
类型主要有：YEAR类型、TIME类型、DATE类型、DATETIME类型和TIMESTAMP类型。

+ YEAR 类型通常用来表示年
+ DATE 类型通常用来表示年、月、日
+ TIME 类型通常用来表示时、分、秒
+ DATETIME 类型通常用来表示年、月、日、时、分、秒
+ TIMESTAMP 类型通常用来表示带时区的年、月、日、时、分、秒

| 类型      | 名称     | 字节 | 日期格式            | 最小值                       | 最大值                     |
| --------- | -------- | ---- | ------------------- | ---------------------------- | -------------------------- |
| YEAR      | 年       | 1    | YYYY或YY            | 1901                         | 2155                       |
| TIME      | 时间     | 3    | HH:MM:SS            | -838:59:59                   | 838:59:59                  |
| DATE      | 日期     | 3    | YYYY:MM:DD          | 1000-01-01                   | 9999-12-03                 |
| DATETIME  | 日期时间 | 8    | YYYY:MM:DD HH:MM:SS | 1000-01-01 00:00:00          | 9999-12-31 23:59:59        |
| TIMESTAMP | 日期时间 | 4    | YYYY:MM:DD HH:MM:SS | 1970-01-01<br/> 00:00:00 UTC | 2038-01-19<br/>03:14:07UTC |

>DATETIM

DATETIME类型在所有的日期时间类型中占用的存储空间最大，总共需要8 个字节的存储空间。在格式上
为DATE类型和TIME类型的组合，可以表示为YYYY-MM-DD HH:MM:SS ，其中YYYY表示年份，MM表示月
份，DD表示日期，HH表示小时，MM表示分钟，SS表示秒。

在向DATETIME类型的字段插入数据时，同样需要满足一定的格式条件。

+ 以YYYY-MM-DD HH:MM:SS 格式或者YYYYMMDDHHMMSS 格式的字符串插入DATETIME类型的字段时，
  最小值为1000-01-01 00:00:00，最大值为9999-12-03 23:59:59。注：以YYYYMMDDHHMMSS格式的数字插入DATETIME类型的字段时，会被转化为YYYY-MM-DD
  HH:MM:SS格式。
+ 使用函数CURRENT_TIMESTAMP() 和NOW() ，可以向DATETIME类型的字段插入系统的当前日期和
  时间。
