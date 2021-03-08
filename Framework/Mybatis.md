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

mybatis_scope