## SpringCloud

微服务架构的4个核心问题？

1. 服务很多，客户端怎么访问
2. 这么多服务，服务之间如何通信
3. 这么多服务，如何治理
4. 某个服务挂了怎么办

解决方案：

Spring Cloud 生态 SpringBoot

1. Spring Cloud NetFlix：一站式解决方案

- 对于一：api网关，zuul组件

- 对于二：Feign --httpclient-- http通信方式，同步、阻塞

- 对于三：服务注册于发现：Eureka

- 对于四：Hystrix熔断机制



2. Apache Dubbo Zookeeper：半自动，需要整合别人的组件

- 对于一：API网关：没有需要融合其他第三方组件或者自己实现

- 对于二：Dubbo 高性能的通信RPC框架

- 对于三：服务注册于发现：Zookeeper

- 对于四：熔断机制没有需要融合第三方组件

3. Spring Cloud Alibaba：一站式解决方案，更简单



万变不离其宗：

1. API
2. Http,RPC
3. 注册于发现
4. 熔断机制

SpringBoot和SpringCloud的关系

- SpringBoot专注于快速方便的开发单个个体微服务
- SpringCloud是关注全局的微服务协调整理治理框架，它将SpringBoot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配置管理、服务发现、熔断器、路由、微代理、事件总线、全局锁、决策竞选
- SpringBoot可以离开SpringCloud独立使用，开发项目，但是SpringCloud离不开SpringBoot，属于依赖关系
- SpringBoot专注于快速、方便的开发单个个体微服务，SpringCloud关注全局的服务治理框架