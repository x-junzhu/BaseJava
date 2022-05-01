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