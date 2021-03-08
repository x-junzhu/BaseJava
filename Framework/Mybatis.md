# Mybatis

## 1 简介

### 1.1 mybatis是什么

- MyBatis 是一款优秀的持久层框架
- 它支持自定义 SQL、存储过程以及高级映射
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

如何获取mybatis?
+ maven仓库
```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```
+ github: https://github.com/mybatis/mybatis-3
+ 中文文档: https://mybatis.org/mybatis-3/zh/index.html

### 1.2 持久化

数据持久化
- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存: **断电即失**
- 持久化方法: 数据库(JDBC), io(文件读取)

为什么需要持久化?  
部分数据不能让它丢失

- 内存太贵: 不能一直放在内存中

### 1.3 持久层

Dao层, Service层, Controller层
+ 完成持久化工作的模块
+ 层的界限十分明显

### 1.4 为什么需要mybatis

+ 方便
+ 传统的jdbc代码太复杂, 简化数据库操作

## 2 第一个mybatis程序

思路: 搭建环境 > 导入mybtis > 编写代码 > 测试

### 2.1 搭建环境

包括mysql数据库和idea项目

mybatis依赖
```xml
<!-- 导入依赖 -->
<dependencies>
    <!-- mysql驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!-- mybatis依赖 -->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    <!-- junit单元测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

### 2.3 引入配置文件

1. 编写mybatis配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?userSSL=false&amp;userUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

2. 编写mybatis工具类
```java
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;
    
    static{
        // 使用mybatis获取SqlSessionFactory对象
        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (Exception e){
            e.printStackTrace();
        }
    }

    /**
     * 通过工厂类获取sqlSession
     * @return
     */
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }


}
```

3. 测试mybatis使用

- 实体类
```java
public class User {
    private int id;
    private String name;
    private String pwd;
}
```
- xxxDao 等价于xxxMapper
```java
public interface UserMapper {

    List<User> getAllUser();
}
```

- xxxDaoimpl 等价于 xxxMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.fzu.dao.UserMapper">
    <select id="getAllUser" resultType="edu.fzu.bean.User">
    select * from user
  </select>
</mapper>
```

### 2.4 测试

注意:   
org.apache.ibatis.binding.BindingException: Type interface edu.fzu.dao.UserMapper is not known to the MapperRegistry.

## 3 CRUD

### 1 namespace

namespace中的包名要和 Dao/mapper 接口的包名一致！

### 2 select

选择，查询语句;

- id : 就是对应的namespace中的方法名；

- resultType：Sql语句执行的返回值！

- parameterType ： 参数类型！



1. 编写接口

```java
public interface UserMapper {
    // 查询所有用户
    List<User> getAllUser();
}
```

2. 编写对应的mapper中的sql语句

```xml
<select id="getAllUser" resultType="edu.fzu.bean.User">
    select * from user
</select>
```

3. 测试接口使用

```java
@Test
public void testUser(){
    // 1. 获取sqlSession
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    // 方式一
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    // 方式二
    List<User> userList = sqlSession.selectList("edu.fzu.dao.UserMapper.getAllUser");
    List<User> allUser = userMapper.getAllUser();

    for (User user: userList){
        System.out.println(user);
    }
    // 2. 关闭sqlSession
    sqlSession.close();
}
```

### 3 insert

```xml
<insert id="addUser" parameterType="edu.fzu.bean.User">
    insert into user (id, name, pwd) values(#{id}, #{name}, #{pwd})
</insert>
```

### 4 update

```java
<update id="updateUser" parameterType="edu.fzu.bean.User">
    update user set name=#{name}, pwd=#{pwd} where id=#{id}
</update>
```

### 5 delete

```java
<delete id="deleteUser" parameterType="java.lang.Integer">
    delete from user where id=#{id}
</delete>
```

**注意: 增删改需要提交事务！**

### 7 万能Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map！**主要解决实体类属性名月数据库字段名不一致的情况.**

```java
public interface UserMapper {
    // ====map使用测试====
    int addUserByMap(Map<String, Object> map);

    User getUserByMap(Map<String, Object> map);
}
```

```xml
<!-- map的使用测试 -->
<insert id="addUserByMap" parameterType="map">
    insert into user (id, name, pwd) values(#{userId}, #{userName}, #{userPwd})
</insert>

<select id="getUserByMap" resultType="edu.fzu.bean.User" parameterType="map">
    select * from user where id=#{myUserId} and name=#{myUserName}
</select>
```

```java
@Test
public void testAddUserByMap(){
    Map<String, Object> map = new HashMap<String, Object>();

    map.put("userId", 190325001);
    map.put("userName", "张小明");
    map.put("userPwd", "zxm123");

    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    int count = userMapper.addUserByMap(map);

    // 提交事务
    sqlSession.commit();
    System.out.println(count);
    sqlSession.close();
}
```

Map传递参数，直接在sql中取出key即可！  【parameterType="map"】

对象传递参数，直接在sql中取对象的属性即可！【parameterType="Object"】

## 4 配置解析

### 4.1 核心配置文件

- mybatis-config.xml
- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息

```xml
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```

### 4.2 环境配置（environments）

MyBatis 可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境**

Mybatis默认的事务管理器就是 JDBC，连接池：POOLED

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
    <!--当前选择为: 开发环境-->
    <environments default="development">
        <!--开发环境-->
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?userSSL=true&amp;userUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="xjz99076"/>
            </dataSource>
        </environment>
        <!--test环境-->
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?userSSL=true&amp;userUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="xjz99076"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

### 4.3 属性（properties）

我们可以通过properties属性来实现引用配置文件

这些属性都是可外部配置且可动态替换的，既可以在典型的 Java 属性文件中配置，亦可通过 properties 元素的子元素来传递。【db.properties】



编写一个配置文件: db-config.properties

```properties
driver=com.mysql.jdbc.Driver

url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8

username=root

password=123456
```

在核心配置文件中引入**(注意引入的顺序)**

```xml
<!-- 引入外部配置文件 -->
<properties resource="db-config.properties"/>
```

### 4.4 类型别名(typeAliases)

- 类型别名是为 Java 类型设置一个短的名字
- 存在的意义仅在于用来减少类完全限定名的冗余

```xml
<!--可以给实体类起别名-->
<typeAliases>
    <typeAlias type="edu.fzu.bean.User" alias="user"/>
</typeAliases>
```

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，比如：

扫描实体类的包，它的默认别名就为这个类的 类名，首字母小写！

```xml
<!-- 给实体类起别名, 减少冗余 -->
<typeAliases>
    <!-- <typeAlias alias="user" type="edu.fzu.bean.User" />-->
    <!-- 在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名 -->
    <package name="edu.fzu.bean"/>
</typeAliases>
```

### 4.5 设置(setting)

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为

### 4.6 其他设置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)

- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)

- plugins插件

  - mybatis-generator-core

  - mybatis-plus

  - 通用mapper

### 4.7 映射器(mappers)

MapperRegistry：注册绑定我们的Mapper文件

>  方式一： 【推荐使用】

```xml
<!-- 每一个mapper.xml都要在核心配置文件中注册 -->
<mappers>
    <mapper resource="sqlmap/UserMapper.xml"/>
</mappers>
```

> 方式二：使用class文件绑定注册

```xml
<!--每一个Mapper.XML都需要在Mybatis核心配置文件中注册！-->
<mappers>
    <mapper class="edu.fzu.mapper.UserMapper"/>
</mappers>
```

**注意点**

- 接口和他的mapper配置文件必须同名！

- 接口和他的mapper配置文件必须在同一个包下！

> 方式三：使用扫描包进行注入绑定

```xml
<!--每一个Mapper.XML都需要在Mybatis核心配置文件中注册！-->
<mappers>
    <package name="edu.fzu.mapper"/>
</mappers>
```

### 4.8 生命周期和作用域

![avatar](picture/mybatis_scope.png)

生命周期，和作用域，是至关重要的，因为错误的使用会导致非常严重的**并发问题**

**SqlSessionFactoryBuilder：**

- 一旦创建了 SqlSessionFactory，就不再需要它了

- 局部变量

**SqlSessionFactory：**

- 说白了就是可以想象为 ：数据库连接池

- SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，***\*没有任何理由丢弃它或重新创建另一个实例。\**** 

- 因此 SqlSessionFactory 的最佳作用域是应用作用域。 

- 最简单的就是使用**单例模式**或者静态单例模式。

**SqlSession**

- 连接到连接池的一个请求！

- SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。

- 用完之后需要赶紧关闭，否则资源被占用！

## 5 解决属性名和字段名不一致的问题

### 5.1 出现的问题

数据库中的字段

```sql
CREATE TABLE `user` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  `pwd` VARCHAR(30) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;
```

实体类中的属性

```java
public class User {
    private int id;
    private String name;
    private String password; // 与数据库中的字段pwd不相同
}
```

解决办法:

1. 起别名

```xml
<select id="getUserById" resultType="edu.fzu.bean.User">
    select id,name,pwd as password from user where id = #{id}
</select>
```

2. resultMap

```xml
<!-- 结果及映射 -->
<resultMap id="userMap" type="user">
    <result column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="password"/>
</resultMap>

<select id="getUserById" resultType="user">
    select * from user where id=#{id}
</select>
```



- `resultMap` 元素是 MyBatis 中最重要最强大的元素

- ResultMap 的设计思想是，对于简单的语句根本不需要配置显式的结果映射，而对于复杂一点的语句只需要描述它们的关系就行了。

- `ResultMap` 最优秀的地方在于，虽然你已经对它相当了解了，但是根本就不需要显式地用到他们。

- 如果世界总是这么简单就好了。

## 6 日志

### 6.1 日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手！

日志工厂

- SLF4J 

- LOG4J 【掌握】

- LOG4J2

- JDK_LOGGING

- COMMONS_LOGGING

- STDOUT_LOGGING  【掌握】

- NO_LOGGING

```xml
<settings>
    <!-- 标准的日志工厂实现 -->
    <!--        <setting name="logImpl" value="STDOUT_LOGGING"/>-->
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

如果要使用LOG4J需要引入一个maven依赖, 同时配置一下日志的输出格式文件properties

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
<version>1.2.17</version>
</dependency>
```

log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/john.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

**简单使用**

1. 在要使用Log4j 的类中，导入包 import org.apache.log4j.Logger;

2. 日志对象，参数为当前类的class

```java
static Logger logger = Logger.getLogger(UserDaoTest.class);
```

3. 日志级别

```java
logger.info("info:进入了testLog4j");

logger.debug("debug:进入了testLog4j");

logger.error("error:进入了testLog4j");
```

## 7 分页

### 7.1 使用Limit分页

```xml
<!-- 分页查询用户 -->
<select id="getUserByLimit" resultMap="userMap">
    select id, name, pwd from user limit #{startIndex}, #{pageSize}
</select>
```



1. 接口

```java
// 分页查询用户
List<User> getUserByLimit(Map<String, Object> map);
```

2. Usermapper.xml

```xml
<!-- 分页查询用户 -->
<select id="getUserByLimit" resultMap="userMap">
    select id, name, pwd from user limit #{startIndex}, #{pageSize}
</select>
```

3. 测试使用

```java
@Test
public void testGetUserByLimit(){
    logger.info("进入testGetUserByLimit方法...");

    Map<String, Object> userMap = new HashMap<String, Object>();
    userMap.put("startIndex", 0);
    userMap.put("pageSize", 2);

    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    List<User> userList = userMapper.getUserByLimit(userMap);

    for (User user: userList){
        System.out.println(user);
    }

    sqlSession.close();
}
```

### 7.2 RowBounds分页(知道即可)

### 7.3 分页插件PageHelper

TODO

## 8 复杂数据关系处理

### 8.1 多对一

- 多个学生，对应一个老师

- 对于学生这边而言， **关联** 多个学生，关联一个老师 【多对一】

- 对于老师而言，**集合** ， 一个老师，有很多学生 【一对多】

1. 学生接口及实体类

```java
public class Student {
    private int id;
    private String name;

    // 学生关联老师
    private Teacher teacher;
}
```



```java
// 获取所有学生信息
public List<Student> getAllStudent();
```

2. StudentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.fzu.mapper.StudentMapper">

    <resultMap id="stuMap" type="student">
        <result column="id" property="id"/>
        <result column="name" property="name"/>
        <!--复杂的属性，我们需要单独处理 对象： association 集合： collection -->
        <association property="teacher" javaType="teacher">
            <id column="tname" property="name"></id>
        </association>
    </resultMap>

    <select id="getAllStudent" resultMap="stuMap">
        select s.id, s.name, t.name tname from student s, teacher t where s.tid=t.id
    </select>

</mapper>
```

3. 测试

```java
@Test
public void testGetStudent(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    StudentMapper studentMapper = sqlSession.getMapper(StudentMapper.class);
    List<Student> studentList = studentMapper.getAllStudent();
    for (Student student: studentList){
        System.out.println(student);
    }

    sqlSession.close();
}
```

### 8.2 多对一

比如：一个老师拥有多个学生！

对于老师而言，就是一对多的关系!

1. Teacher实体类和接口

```java
public class Teacher {
    private int id;
    private String name;

    private List<Student> studentList;
}
```

```java
public interface TeacherMapper {
    public List<Teacher> getAllTeacher();
}
```

2. TeacherMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.fzu.mapper.TeacherMapper">

    <resultMap id="teacherMap" type="teacher">
        <result column="tid" property="id"/>
        <result column="tname" property="name"/>
        <collection property="studentList" ofType="student">
            <result column="sid" property="id"/>
            <result column="sname" property="name"/>
        </collection>
    </resultMap>

    <select id="getAllTeacher" resultMap="teacherMap">
        select t.id tid, t.name tname, s.id sid, s.name sname from teacher t, student s where t.id=s.tid
    </select>
</mapper>
```

3. 测试

```java
@Test
public void testGetTeacher(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    TeacherMapper teacherMapper = sqlSession.getMapper(TeacherMapper.class);
    List<Teacher> teacherList = teacherMapper.getAllTeacher();

    for (Teacher teacher : teacherList) {
        System.out.println(teacher);

    }

    sqlSession.close();
}
```

**小结**

- 关联 - association  【多对一】

- 集合 - collection  【一对多】

- javaType  &  ofType

  1. JavaType 用来指定实体类中属性的类型

  2. ofType 用来指定映射到List或者集合中的 pojo类型，泛型中的约束类型！

## 9 动态SQL

实体类

```java
public class Blog {
    private String id;
    private String title;
    private String author;
    private Date createTime;
    // 浏览量
    private int views;
}
```

### 9.1 if

```xml
<select id="queryBlogByCondition" parameterType="map" resultType="blog">
    select * from blog where 1=1
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</select>
```

### 9.2 choose (when, otherwise)

```xml
<!-- where标签在内部没有条件满足的时候会自动去掉关键字where -->
<select id="queryBlogByChoose" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <choose>
            <when test="title != null">
                title=#{title}
            </when>
            <when test="author != null">
                and author=#{author}
            </when>
            <otherwise>
                and views=#{views}
            </otherwise>
        </choose>
    </where>
</select>
```

### 9.3 set

```xml
<update id="updateBlog" parameterType="map">
    update blog
    <set>
        <if test="title != null">
            title=#{title},
        </if>
        <if test="author != null">
            author=#{author}
        </if>
    </set>
    where id=#{id}
</update>
```

**所谓的动态SQL，本质还是SQL语句 ， 只是我们可以在SQL层面，去执行一个逻辑代码**

### 9.4 SQL片段

- 使用SQL标签抽取公共的部分

```xml
<sql id="if-title-author">
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</sql>
```

- 在需要使用的地方使用Include标签引用即可

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <include refid="if-title-author"></include>
    </where>
</select>
```

注意事项：

- 最好基于单表来定义SQL片段！

- 不要存在where标签

### 9.5 foreach

1. 接口

```java
// 通过foreach查询信息
public List<Blog> queryBlogByForeach(Map<String, Object> map);
```

```sql
select * from user where 1=1 and (id=1 or id=2 or id=3)
```



```xml
<select id="queryBlogByForeach" parameterType="map" resultType="blog">
    select * from blog
    <where>
        <foreach collection="ids" item="id" open="(" separator="or" close=")">
            id=#{id}
        </foreach>
    </where>
</select>
```

动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了

## 10 Mybatis缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

- 默认情况下，只有一级缓存开启（SqlSession级别的缓存，也称为本地缓存）

- 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。

- 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

### 10.1 一级缓存

- 一级缓存也叫本地缓存： SqlSession

- 与数据库同一次会话期间查询到的数据会放在本地缓存中。

- 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

测试步骤：

1. 开启日志！

2. 测试在一个Sesion中查询两次相同记录

3. 查看日志输出

```java
@Test
public void testGetAllUserInfo(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    User user1 = userMapper.getUserById(190325001);
    User user2 = userMapper.getUserById(190325001);

    System.out.println(user1 == user2); // 结果时true

    sqlSession.close();
}
```

缓存失效的情况:

1. 查询不同的东西

2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存！

3. 查询不同的Mapper.xml

4. 手动清理缓存！



小结：一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段！

一级缓存就是一个Map。

### 10.2 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存

- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；

工作机制

- 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；

- 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；

- 新的会话查询信息，就可以从二级缓存中获取内容；

- 不同的mapper查出的数据会放在自己对应的缓存（map）中；



步骤：

1. 开启全局缓存

```xml
  <!--显示的开启全局缓存: 在mybatis-config.xml -->
  <setting name="cacheEnabled" value="true"/>
```

2. 在要使用二级缓存的Mapper中开启

也可以自定义参数

```xml
<!-- 在当前mapper配置中开启缓存-->
<cache eviction="FIFO"
        flushInterval="60000"
        size="512"
        readOnly="true" type="org.mybatis.caches.ehcache.EhcacheCache"/>
```



3. 测试

```java
@Test
public void testGetAllUserInfoByCache(){
    SqlSession sqlSession1 = MybatisUtils.getSqlSession();
    UserMapper userMapper1 = sqlSession1.getMapper(UserMapper.class);
    User user1 = userMapper1.getUserById(190325001);
    sqlSession1.close();

    /*
        工作机制
        1. 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
        2. 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
        3. 新的会话查询信息，就可以从二级缓存中获取内容；
        4. 不同的mapper查出的数据会放在自己对应的缓存（map）中；
         */
    SqlSession sqlSession2 = MybatisUtils.getSqlSession();
    UserMapper userMapper2 = sqlSession2.getMapper(UserMapper.class);
    User user2 = userMapper2.getUserById(190325001);

    System.out.println(user1 == user2);
    sqlSession2.close();

}
```



1. 问题:我们需要将实体类序列化！否则就会报错！

```java
Caused by: java.io.NotSerializableException: edu.fzu.pojo.User
```

小结：

\- 只要开启了二级缓存，在同一个Mapper下就有效

\- 所有的数据都会先放在一级缓存中；

\- 只有当会话提交，或者关闭的时候，才会提交到二级缓冲中！