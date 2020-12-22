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

# Eureka

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
           # 服务器地址,集群地址
           defaultZone: http://eureka01.66-77.cc:7001/eureka/
       instance:
         # 修改注册时使用的默认描述信息
         instance-id: SpringCloud-provider-dept-8001
     ```

- 服务调用者

  1. 依赖

     ```xml
     <!--eureka-client 包含了 Ribbon的依赖-->
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
         # 消费者不用注册自己
         register-with-eureka: false
         service-url:
           # 注册中心地址
           defaultZone: http://eureka01.66-77.cc:7001/eureka,http://eureka02.66-77.cc:7002/eureka,http://eureka03.66-77.cc:7003/eureka
     ```

  4. 调用

     配置RestTemplate

     ```java
     @Configuration
     public class RestTemplateConfig {
         @Bean
         /**对RestTemplate启动负载均衡,基于Ribbon*/
         @LoadBalanced
         public RestTemplate restTemplate(){
             return new RestTemplate();
         }
         @Bean
         /**
          * 配置一下负载均衡的规则
          * 默认使用的是RoundRobinRule:轮询
          * AvailabilityFilteringRule:可用过滤后轮询
          * RandomRule:随机
          * RetryRule:一定时间内重试
          * */
         public IRule configRule(){
             return new RandomRule();
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
         /**将前缀改为负载均衡总获取到的服务id,对应的是服务提供者的spring.application.name配置*/
         private static final String REST_URL_PREFIX="http://SPRINGCLOUD-PROVIDER-DEPT";
     
         @PostMapping("/dept")
         public boolean addDept(Dept dept){
             return restTemplate.postForObject(REST_URL_PREFIX + "/dept", dept, Boolean.class);
         };
     }
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

### Zookeeper与Eureka的对比

根据CAP原则

- Zookeeper保证的是CP
  - 有leader机制,leader挂掉一个要重新选举leader,过程中服务不可用
- Eureka保证的是AP
  - 节点之间平等,挂掉一个节点就换另一个,直到全部都挂掉

**Eureka可以很好的应对网络故障节点失联的情况,不会服务瘫痪~**

# Ribbon

### Ribbon是什么

- SpringCloudRibbon是基于NetflixRibbon实现的**客户端负载均衡工具**

- 简单的说，Ribbon是Netflix发布的开源项目， 主要功能是提供客户端的软件负载均衡算法，将NetFlix的中间层服务连接在一起.Ribbon的客户端组件提供一系列完整的配置项如:连接超时、重试等等。简单的说，就是在配置文件中列出LoadBalancer (简称LB:负载均衡)后面所有的机器，Ribbon会 自动的帮助你基于某种规则(如简单轮询，随机连接等等)去连接这些机器。我们也很容易使用Ribbon实现自定义的负载均衡算法!

### 负载均衡

- 负载均衡:将用户的请求平摊的分配到多台服务器上,从而实现系统的高可用(HA)
- Nginx,Lvs等,Dubbo与SpringCloud中也提供了负载均衡
- 分类:
  - 集中式LB
    - 在服务的消费方与提供方中间使用独立的LB设施,例如Nginx
  - 进程式LB
    - 将LB逻辑集成到消费方,消费方从注册中心的值哪些服务可用,然后从中选出一个合适的服务器
    - **Ribbon数据进程式LB**,是一个类库,集成于消费方

### Ribbon能干什么

- 与Eureka搭配使用
- 提供基于RestTemplate的HTTP客户端并且支持服务负载均衡功能

# Feign

feign是声明式的web service客户端，它让微服务之间的调用变得更简单了,类似controller调用service,只需要创建一个接口，然后添加注解即可!

feign,主要是社区,大家都习惯面向接口编程。这个是很多开发人员的规范。

### 调用微服务访问两种方法

- 微服务名字[Ribbon]
- 接口和注解[Feign,Dubbo]

Feign底层还是调用的Ribbon

### Feign的使用

1. 导入依赖

   ```xml
   <!-- feign依赖 -->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-openfeign</artifactId>
     <version>2.2.6.RELEASE</version>
   </dependency>
   ```

2. 启动类上标记**@EnableFeignClients(basePackages = {"com.sowevo.springcloud.service"})**并指定FeignClients的包

3. 修改代码

   ```java
   //新建接口,要与provider中提供的服务对应
   @Component
   /**value 对应的是服务提供者的spring.application.name配置*/
   @FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT-HYSTRIX")
   public interface DeptService {
       @PostMapping("/dept")
       boolean addDept(Dept dept);
       @GetMapping("/dept/{id}")
       Dept queryDeptById(@PathVariable("id") Long id);
       @GetMapping("/dept")
       List<Dept> queryAll();
   }
   ===========================
   //   Controller中注入接口,进行调用
   @RestController
   @RequestMapping("/consumer")
   public class DeptConsumerController {
       @Autowired
       DeptService deptService;
       @PostMapping("/dept")
       public boolean addDept(Dept dept){
           return deptService.addDept(dept);
       };
       @GetMapping("/dept/{id}")
       public Dept queryDeptById(@PathVariable("id") Long id){
           return deptService.queryDeptById(id);
       };
       @GetMapping("/dept")
       public List<Dept> queryAll(){
           return deptService.queryAll();
       };
   }
   ```

# Hystrix

### 什么是Hystrix

Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时，异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败,避免级联故障,以提高分布式系统的弹性。

“断路器”本身是一种开关装置， 当某个服务单元发生故障之后,通过断路器的故障监控(类似熔断保险丝)，向调用方返回一个服务预期的，可处理的备选响应(FallBack) ，而不是长时间的等待或者抛出调用方法无法处理的异常，这样就可以保证了服务调用方的线程不会被长时间，不必要的占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩

### 能干嘛

- 服务降级
- 服务熔断
- 服务限流
- 服务监控
- ...

### 服务熔断与服务降级

- 服务熔断
  - 服务熔断在服务端处理
  - 某个服务超时或异常,引起熔断

- 服务降级
  - 服务降级在客户端处理
  - 从整体请求考虑,当某个服务熔断或关闭后,服务不再被调用
  - 此时在客户端调用的是一个缺省值

### 服务熔断使用

- 在客户端,对一个方法提供一个备选

1. 导入依赖

   ```xml
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
     <version>2.2.6.RELEASE</version>
   </dependency>
   ```

2. 启动类上标记**@EnableHystrix**

3. 修改代码   使用@HystrixCommand对异常方法指定备用方法,当发生异常时,会使用这个方法

   ```java
   @RestController
   public class DeptController {
       @Autowired
       DeptService deptService;
       // 使用@HystrixCommand对异常方法指定备用方法,当发生异常时,会使用这个方法
       @HystrixCommand(fallbackMethod = "hystrixQueryDeptById")
       @GetMapping("/dept/{id}")
       public Dept queryDeptById(@PathVariable("id") Long id){
           Dept dept = deptService.queryDeptById(id);
           if (dept==null){
               throw new RuntimeException("发生了异常!!!");
           }
           return dept;
       };
       public Dept hystrixQueryDeptById(@PathVariable("id") Long id){
           return new Dept().setDeptno(id).setDname("此ID不存在啦").setDb_source("不存在");
       };
   }
   ```

### 服务降级使用

- 在服务端,对接口提供一个备选接口,有用到feign

1. 导入依赖

   ```xml
   <!-- hystrix依赖 -->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
   </dependency>
   <!-- feign依赖 -->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-openfeign</artifactId>
     <version>2.2.6.RELEASE</version>
   </dependency>
   ```

2. 启动类上加上注解**@EnableHystrix**

3. 修改代码

   ```java
   //实现FallbackFactory,并在create中返回FeignClient接口的备选实现
   @Component
   public class DeptServiceFallbackFactory implements FallbackFactory {
       @Override
       public DeptService create(Throwable throwable) {
           return new DeptService() {
               @Override
               public boolean addDept(Dept dept) {
                   return false;
               }
               @Override
               public Dept queryDeptById(Long id) {
                   return new Dept().setDeptno(Long.MAX_VALUE).setDname("服务不可用").setDb_source("");
               }
               @Override
               public List<Dept> queryAll() {
                   return Collections.singletonList(new Dept().setDeptno(Long.MAX_VALUE).setDname("服务不可用").setDb_source(""));
               }
           };
       }
   }
   ======================
   //FeignClient接口中指定自己实现的FallbackFactory
   @Component
   /*
   value 对应的是服务提供者的spring.application.name配置
   fallbackFactory 指定的是HYSTRIX的FallbackFactory备选实现
   */
   @FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT-HYSTRIX",fallbackFactory = DeptServiceFallbackFactory.class)
   public interface DeptService {
       @PostMapping("/dept")
       boolean addDept(Dept dept);
       @GetMapping("/dept/{id}")
       Dept queryDeptById(@PathVariable("id") Long id);
       @GetMapping("/dept")
       List<Dept> queryAll();
   }  
   ```

### Hystrix Dashbord

- 能配置,能看懂就行...

# 路由网关Zuul

概述:

### 什么是Zuul?

Zuul包含了对请求的路由和过滤两个最主要的功能:

其中路由功能负责将外部请求转发到具体的微服务实例上，是实现外部访问统一入口的基础， 而过滤器功能则负责对请求的处理过程进行干预，是实现请求校验,服务聚合等功能的基础。Zuul和Eureka进行整合, 将Zuul自身注册为Eureka服务治理下的应用,同时从Eureka中获得其他微服务的消息，也即以后的访问微服务都是通过Zuul跳转后获得。

**注意: Zuul服务最终还是会注册进Eureka**

提供:代理+路由+过滤三大功能!(貌似Nginx都能做撒...)

### Zuul能干嘛?

- 路由
- 过滤

### Zuul的使用

1. 导入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
       <version>2.2.6.RELEASE</version>
   </dependency>
   ```

2. 启动类上标记**@EnableZuulProxy**

3. 修改配置

   ```yaml
   # 需要注入eureka才能对其他服务进行路由转发
   eureka:
     client:
       service-url:
         # 集群部署的话,写多个用","分割
         defaultZone: http://eureka01.66-77.cc:7001/eureka,http://eureka02.66-77.cc:7002/eureka,http://eureka03.66-77.cc:7003/eureka
     instance:
       # 修改注册时使用的默认描述信息
       instance-id: ${spring.application.name}-${server.port}
       hostname: zuul.66-77.cc
   ```

4. 路由定制

   ```yaml
   zuul:
     routes:
       # 服务名到路径的映射
       consumer.serviceID: springcloud-consumer-dept-feign
       consumer.path: /consumer/**
       provider.serviceID: springcloud-provider-dept-hystrix
       provider.path: /provider/**
     ignored-services:
       # 禁用原本的服务名访问 使用"*"可以隐藏全部
       - springcloud-consumer-dept-feign
       - springcloud-provider-dept-hystrix
   ```

# SpringCloudConfig

Spring Cloud Config项目是一个解决分布式系统的配置管理方案。它包含了Client和Server两个部分，server提供配置文件的存储、以接口的形式将配置文件的内容提供出去，client通过接口获取数据、并依据此数据初始化自己的应用。

### 服务端:一盒新的服务

1. 导入依赖

   ```xml
   <!-- Spring-Cloud-Config-Server -->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-config-server</artifactId>
     <version>2.2.6.RELEASE</version>
   </dependency>
   ```

2. 启动类上标记**@EnableConfigServer**

3. 配置

   ```yaml
   server:
     port: 3344
   spring:
     application:
       name: SpringCloud-Config-Server
     cloud:
       config:
         server:
           git:
             # git地址,使用git协议的话,需要给主机加上github的指纹等配置,比较麻烦
             uri: https://github.com/Sowevo/SpringCloud-Config.git
             # 默认使用的分支
             default-label: main
             # 认证的账号密码,因为是个私有的git地址,需要认证
             password: *********
             username: *********
             # 可以根据项目名建一个二级文件夹,便于管理
             search-paths: '{application}'
             # 启动时克隆项目
             clone-on-start: true
   ```

### 客户端

已有的服务加入从远端获取配置文件的功能

1. 导入依赖

   ```xml
   <!-- Spring-Cloud-Config-Client -->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-config</artifactId>
     <version>2.2.6.RELEASE</version>
   </dependency>
   ```

2. 新增bootstrap.yaml

   ```yaml
   spring:
     cloud:
       config:
         # 指定注册中心Config-Server的地址
         uri: http://config.66-77.cc:3344/
     application:
       # 指定服务名称,对应的是配置仓库中的二级文件夹,需要在服务端加上search-paths: '{application}'
       name: consumer-dept
   ```

3. 配置文件加入git仓库







