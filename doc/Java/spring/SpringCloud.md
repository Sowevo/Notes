# 微服务架构的4个问题

- 这么多服务，客服端怎么去访问   Api网关
- 这么多服务间，怎么进行通信      服务间通信
- 这么多服务，怎么治理                 注册中心
- 服务器死机了，怎么解决？         熔断机制

# 可选方案

- Api网关
- 服务间通信
  - Feign
  - Ribbon
  - Dubbo
- 注册中心
  - Eureka
  - Nacos
  - Zookeeper
  - Consul
  - Etcd
- 熔断机制
  - Hystrix
  - Sentinel

# 解决方案

SpringCloud生态:

1. SpringCloud Netflix 一站式解决方案:停止维护了
   1. Api网关         Zuul组件
   2. 服务间通信   Feign==>HttpClient
   3. 注册中心       Eureka
   4. 熔断机制       Hystrix
2. Apache Dubbo Zookeeper 半自动解决
   1. Api网关       没有,第三方或自己实现
   2. 服务间通信 Dubbo RPC同心框架
   3. 注册中心     Zookeeper
   4. 熔断机制     没有,找第三方的
3. SpringCloud Alibaba一站式解决方案:新的

# 相关问题

### 什么是微服务

最近流行的架构思想,马丁.福勒提出的

核心就是将传统的单体项目,根据业务拆分为一个一个的服务,彻底的去耦合,每一个服务处理单个业务,一个服务处理一件事,能够单独的启动或部署

### 微服务与微服务架构

**微服务**

微服务指的是服务的大小,关注的是一个点,是具体解决某个问的的一个服务应用

**微服务架构**

一种服务架构,他提倡将单一应用程序划分成一组小的服务,服务之间相互协调,相互配合,每个服务运行在独立的进程中服务间采用具体的通信协议进行写作,能够单独的部署到生产环境中

### 微服务的优点与缺点

**优点**

- 服务足够内聚,足够小,代码易于理解
- 开发简单,开发效率高,一个服务可能只干一件事
- 微服务能够被小团队单独开发,这个团队由2~5个开发人员组成
- 微服务时松耦合的,具有功能性的意义,无论在开发阶段还是部署阶段都是独立的
- 微服务可以使用不同的语言开发
- 易于和第三方集成,微服务允许容易且灵活的方式集成自动部署,通过持续集成工具 如jenkins,hudson,bamboo
- 微服务易于被开发人员理解,修改和维护
- 微服务允许利用最新融合最新的技术
- 只有业务逻辑的代码,不会有Html,css或其他前端页面
- 没个微服务都有自己的存储能力,可以有自己的数据库,也可以有统一的数据库

**缺点**

- 要出李分布式的复杂性
- 运维成本增加
- 系统部署依赖
- 服务间通信成本
- 数据一致性
- 系统集成测试
- 性能监控...

### 微服务技术栈有哪些

| 微服务条目        | 落地技术                                                  |
| :---------------- | --------------------------------------------------------- |
| 服务开发          | Spring,SpringMVC,SpringBoot等                             |
| 服务配置与管理    | Archaius,Diamond                                          |
| 服务注册与发现    | Eureka,Consul,Zookeeper                                   |
| 服务调用          | Rest,RPC,gPRC                                             |
| 服务熔断          | Hystrix,Rnvoy                                             |
| 负载均衡          | Ribbon,Nginx                                              |
| 服务接口调用      | Fegin等                                                   |
| 消息队列          | Kafka,RabbitMQ,ActiveMQ等                                 |
| 服务配置中心      | SpringCloudConfig,Chef等                                  |
| 服务路由(Api网关) | Zuul等                                                    |
| 服务监控          | Zabbix,Nagios,Metrics,Specatator等                        |
| 全链路追踪        | Zipkin,Brave,Dapper等                                     |
| 服务部署          | Docker,OpenStack,Kubernetes等                             |
| 数据流操作开发包  | SpringCloudStream(封装Redis,RabbitMQ,Kafka等发送接收消息) |
| 事件消息总线      | SpringCloudBus                                            |

### 为什么选择SpringCloud

- 选型依据

  - 整体解决方案和框架成熟度
  - 社区热度
  - 可维护性
  - 学习曲线

- 当前各大公司采用的微服务有哪些

  - 阿里:dubbo+HFS
  - 京东:JSF
  - 新浪:Motan
  - 当当:Dubbox
  - .....

- 各微服务框架对比

  | 功能点/服务框架 |             SpringCloud Netflix              |                            Motan                             |           gRPC            |  Thritf  |  Dubbo/Dubbox  |
  | :-------------: | :------------------------------------------: | :----------------------------------------------------------: | :-----------------------: | :------: | :------------: |
  |    功能定位     |               完整的微服务框架               | RPC框架,整合了ZK或Console,实现了集群环境的基本的服务注册/发现 |          RPC框架          | RPC框架  |    服务框架    |
  |    REST支持     |                是,Ribbon支持                 |                              否                              |            否             |    否    |       否       |
  |     RPC支持     |                      否                      |                              是                              |            是             |    是    |       是       |
  |   多语言支持    |                是,REST都支持                 |                              否                              |            是             |    是    |       否       |
  |    负载均衡     |                      是                      |                              是                              |            否             |    否    |       是       |
  |    配置服务     |      Netflix Archaius,SpringCloudConfig      |                        是(Zookeeper)                         |            否             |    否    |       否       |
  | 服务调用链监控  |                   是(zuul)                   |                              否                              |            否             |    否    |       否       |
  |   高可用/容错   |              是(Hystrix+Ribbon)              |                              是                              |            否             |    否    |       是       |
  |    典型应用     |                   Netflix                    |                             Sina                             |          Google           | Facebook |                |
  |   社区活跃度    |                      高                      |                             一般                             |            高             |   一般   |  最近重启维护  |
  |    学习难度     |                      中                      |                              低                              |            高             |    高    |       低       |
  |   文档丰富度    |                      高                      |                             一般                             |           一般            |   一般   |       高       |
  |      其他       | SpringCloudBus为我们的应用带来了更多管理端点 |                           支持降级                           | Netflix在其内部使用了gRPC | IDL定义  | 用的公司比较多 |

# SpringCloud概述

### SpringCloud与SpringBoot的关系

- SpringBoot专注于快速开发单个个体微服务
- SpringCloud是关注全局的微服务协调治理框架,它将SpringBoot开发的一个个单体微服务整合并管理起来,为各个微服务提供:配置管理,服务发现,断路器,路由,微代理,事件总线,全局锁,决策竞选,分布式会话等集成服务
- SpringBoot可以离开SpringCloud独立使用,开发项目,但是SpringCloud离不开SpringBoot,依赖SpringBoot
- SpringBoot专注于快速.方便的开发单个个体微服务,SpringCloud关注全局的服务治理框架

### SpringCloud能干嘛

- Distributed/versioned configuration (分布式/版本控制配置)
- Service registration and discovery (服务注册与发现)
- Routing (路由)
- Service-to-service calls (服务到服务的调用)
- Load balancing (负载均衡配置)
- Circuit Breakers (断路器)
- Distributed messaging (分布式消息管理)
- ...

### SpringCloud版本号

- 取自伦敦地铁站

  SpringBoot与SpringCloud版本对应

  | Spring Cloud             | Spring Boot                                    |
  | ------------------------ | ---------------------------------------------- |
  | Angel版本                | 兼容Spring Boot 1.2.x                          |
  | Brixton版本              | 兼容Spring Boot 1.3.x，也兼容Spring Boot 1.4.x |
  | Camden版本               | 兼容Spring Boot 1.4.x，也兼容Spring Boot 1.5.x |
  | Dalston版本、Edgware版本 | 兼容Spring Boot 1.5.x，不兼容Spring Boot 2.0.x |
  | Finchley版本             | 兼容Spring Boot 2.0.x，不兼容Spring Boot 1.5.x |
  | Greenwich版本            | 兼容Spring Boot 2.1.x                          |
  | Hoxtonl版本              | 兼容Spring Boot 2.2.x                          |

### [参考文档](https://www.springcloud.cc/)

# SpringCloud

### 简单的RestTemplate实现服务调用

- Consumer

  配置类

  ```java
  @Configuration
  public class RestTemplateConfig {
      @Bean
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  }
  ```

  调用RestTemplate

  ```java
  @RestController
  @RequestMapping("/consumer")
  public class DeptConsumerController {
      @Autowired
      RestTemplate restTemplate;
  
      private static final String REST_URL_PREFIX="http://127.0.0.1:8001";
  
      @PostMapping("/dept")
      public boolean addDept(Dept dept){
          return restTemplate.postForObject(REST_URL_PREFIX + "/dept", dept, Boolean.class);
      };
  }
  ```

- Provider

  正常提供Rest的接口

- 两个服务拥有共同的API包,其中包含Model的定义

### Eureka

- 三个角色
  - Eureka Service:Eureka的注册中心
  - Service Provider:提供服务的程序
  - Service Consumer:消费服务的程序
- 自我保护机制
  - Eureka客户端大面积失联时,Eureka会认为可能是网络原因导致的异常,进入自我保护模式,此时不再注销失联的客户端,当网络恢复后,会自动退出此模式
  - 生产环境推荐开启
  - 开发中遇到的比较多,可以关掉
  - `eureka.server.enable-self-preservation: false`进行关闭

### 使用Eureka实现服务间的调用

- Eureka Service的部署

  1. 依赖

  ```xml
  <!--引入eureka-server依赖-->
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    <version>2.2.6.RELEASE</version>
  </dependency>
  ```

  2. 注解...启动类上标记**@EnableEurekaServer**即可

  3. 配置

  ```yaml
  ############单机的配置#############
  server:
    port: 7001
  # eureka的配置
  eureka:
    instance:
      hostname: bj2.sowevo.com # 主机名
      home-page-url-path: /eureka
    client:
      register-with-eureka: false # 是否在eureka服务器上注册
      fetch-registry: false       # 是否从eureka服务器上获取信息
      service-url:
        defaultZone: http://bj2.sowevo.com:7001/eureka
  ############集群的配置#############
  server:
    port: 7001
  # eureka的配置
  eureka:
    instance:
      hostname: eureka01.66-77.cc # 主机名
    client:
      register-with-eureka: false # 是否在eureka服务器上注册
      fetch-registry: false       # 是否从eureka服务器上获取信息
      service-url:
              defaultZone: http://eureka01.66-77.cc:7001/eureka,http://eureka02.66-77.cc:7002/eureka,http://eureka03.66-77.cc:7003/eureka
  ```

- 服务提供者

  1. 依赖

     ```xml
     <!-- 导入eureka的客户端 -->
     <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
       <version>2.2.6.RELEASE</version>
     </dependency>
     ```

  2. 启动类上标记**@EnableEurekaClient**

  3. 配置文件

     ```yaml
     eureka:
       client:
         service-url:
           # 服务器地址,集群填一个就行
           defaultZone: http://eureka01.66-77.cc:7001/eureka/
       instance:
         # 修改注册时使用的默认描述信息
         instance-id: SpringCloud-provider-dept-8001
     ```

### CAP原则

- 对比数据库的ACDI
  - ACDI:原子性,一致性,持久性,隔离性
  - CAP
    - C:强一致性
    - A:可用性
    - P:分区容错性
- CAP理论核心[参考文章](https://www.hollischuang.com/archives/666)
  - 一个分布式系统不能同时很好的满足CAP:强一致性,可用性,分区容错性这三个需求,只能三选二
    - CA:分布式系统必然存在多个分区的情况,舍弃P,就不是分布式了
    - CP:牺牲可用性,保证一致性:例如支付宝
    - AP:牺牲强一致性,保证最终可用性,保证可用性:例如12306...

### Zookeeper与Eureka的区别

根据CAP原则

- Zookeeper实现的是CP
- Eureka实现的是AP

