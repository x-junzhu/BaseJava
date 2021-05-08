## 谷粒在线教育

> 1. 跨域问题

通过一个地址去访问另一个地址的时候，这个过程中如果有三个地方的任何一个地方不一样

访问协议，如http https

ip地址 

端口号

解决方式：

- 在后端的接口Controller上加上注解（常用）

```java
@RestController
@CrossOrigin // 跨域注解
@RequestMapping("/eduservice/user")
public class EduLoginController {

    @PostMapping("login")
    public ResponseResult login(){
        return ResponseResult.ok().add("token", "admin");
    }
}
```

- 网关(SpringCloud)

> 2. 排除数据库资源加载

```java
Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.
```

当某个模块中不需要加载数据库的信息时，可以排除掉数据库的资源加载，在启动类上加上以下属性

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)// 排除数据库资源加载
@ComponentScan(basePackages = {"edu.fzu"})//加载整个工程中的配置类(即带有Configuration注解的类)
public class OssApplication {
    public static void main(String[] args) {
        SpringApplication.run(OssApplication.class, args);
    }
}
```



