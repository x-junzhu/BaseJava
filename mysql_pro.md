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



### 8.3 HAVING的使用
