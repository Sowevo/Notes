# SpringBoot

## 什么是微服务

- 先读一下微服务的[介绍](../../martinfowler-microservices-zh-cn.md)

## 第一个spring程序

- [官网](https://start.spring.io/)提供的在线生成方式,下载导入IDEA使用
- IDEA集成的生成方式,直接在IDEA中使用

## 原理初探

### 自动装配

- pom.xml

  - 依赖在父工程中
  - 我们引入一些Springboot依赖时,不需要指定版本,因为有这些版本仓库配置

- 启动器

  - ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

  - Springboot的启动场景:比如`spring-boot-starter-web`,他会帮我们导入web环境的依赖

  - springboot会将所有的功能场景,都变成一个一个的启动器,如果要使用什么功能,就只需要找到对应的启动器`start`

- 主程序

  ```java
  @SpringBootApplication//标注这个类时一个SpringBoot的应用
  public class Springboot01HelloworldApplication {
    public static void main(String[] args) {
      //将SpringBoot启动
      SpringApplication.run(Springboot01HelloworldApplication.class, args);
    }
  }
  ```

  - 注解
    - @SpringBootConfiguration:Spring的配置
      - @Configuration:Spring配置类
        - @Component:这是一个Spring组件
    - @EnableAutoConfiguration:自动配置
      - @AutoConfigurationPackage:自动配置包
        - @Import(AutoConfigurationPackages.Registrar.class):自动配置`包注册`
      - @Import(AutoConfigurationImportSelector.class):自动导入选择器
        - AutoConfigurationImportSelector.getCandidateConfigurations
          - 从`META-INF/spring.factories`中找到自动配置
          - 根据不同的自动配置会根据自己需要的包有没有导入为条件,决定是否生效

- <font color=red>自动装配总结!</font>

  0. 自动装配主要在spring-boot-autoconfigure包
  1. 启动时,从包里的配置文件中加载所有的自动配置类XXXAutoConfiguration
  2. 判断是否启用:根据具体的自动配置类上面的注解
     1. 是不是WEB项目
     2. 自动配置类的依赖包有没有引入
     3. 配置文件中相关属性有没有启用
  3. 使用@ConfigurationProperties注解从配置文件中提取相关配置文件
  4. 配置完成后给加入到Spring容器里面
  5. 以前需要手动做的配置,自动配置类都给做了

### SpringApplication.run启动流程

![](https://up.sowevo.com/img/20201215153540.png)

## 配置文件

### 1. [官方的配置文档](https://docs.spring.io/spring-boot/docs/2.4.1/reference/htmlsingle/#common-application-properties)

### 2. 配置文件是与自动配置类XXXAutoConfiguration的配置类一一对应的

- 由自动配置类的@EnableConfigurationProperties注解指定的配置类XXXProperties

### 3. 从配置文件中获取值

- @ConfigurationProperties:将配置文件中的属性,映射到这个组件中,需要首先是组件(@Component),要配置参数`prefix`

- @Value注解@Value("${name}")

  | 注解                 | @ConfigurationProperties | @Value     |
  | -------------------- | ------------------------ | ---------- |
  | 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
  | 松散绑定（松散语法） | 支持                     | 不支持     |
  | SpEL                 | 不支持                   | 支持       |
  | JSR303数据校验       | 支持                     | 不支持     |
  | 复杂类型封装         | 支持                     | 不支持     |
  
- 相关解释

  - @ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加
  - 松散绑定last-name可以绑定lastName
  - JSR303 使用
  - 复杂类型封装，yml中可以封装对象 ， 使用value就不支持

- 结论

  - yml和properties都可以获取到值
  - 获取单个值得话,用@vlaue
  - 复杂对象的话,用@ConfigurationProperties

### 4. yaml中占位符的功能

```yaml
# 占位符
server:
  # 启动时随机端口号?
  port: ${random.int(10000,20000)}
person:
  name: 小花-${random.uuid}
  age: ${random.long(1,100)}
```

### 5. 多环境切换

- ```yaml
  # 主配置
  spring:
    profiles:
      # 选择使用哪一个环境的配置
      active: mraw
  ---
  # 选配一:测试环境的端口
  server:
    port: 2222
  spring:
    config:
      activate:
        on-profile: test
  ---
  # 选配二:开发环境的端口
  server:
    port: 1111
  spring:
    config:
      activate:
        on-profile: dev
  ---
  # 选配二:模拟环境的端口
  server:
    port: 3333
  spring:
    config:
      activate:
        on-profile: mraw
  ```

## <font color=red>配置文件&自动配置类&配置类</font>

- 配置文件

- XXXAutoConfiguration:自动配置类

- XXXProperties:配置类

  配置文件中所有的配置都对应一个XXXProperties配置类

  所有配置类都对应一个XXXAutoConfiguration自动配置类

  从而实现自动配置,自动加载配置文件

## SpringBoot WEB开发

### 静态资源

- 是否有自定义配置:`spring.mvc.static-path-pattern`,有的话按照自定义的来

- 请求的路由是不是webjars的资源,是的话访问webjars的资源(不好用)

- 最后按照如下顺序进行访问
  
  classpath:/META-INF/resources/
  classpath:/resources/
  classpath:/static/
  classpath:/public/

### 首页

- 从前面的静态资源目录中寻找index.html文件

### 模板引擎Thymeleaf

- [官方文档](https://www.thymeleaf.org/documentation.html)

- 导入Thymeleaf的启动器

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
  ```

- 模板位置: html页面放入`classpath:/templates/`目录下

- 首先要引入Thymeleaf的命名空间

  ```html
  <html xmlns:th="http://www.thymeleaf.org">
  ```

- 语法

  ```html
  <!--th:text  文本-->
  <div th:text="${msg}"></div>
  <!--th:utext 文本,进行转义-->
  <div th:utext="${msg}"></div>
  <!--th:each  对象遍历-->
  <h3 th:each="name:${names}" th:text="${name}"></h3>
  <!--使用th:href="@{}"接管静态资源-->
  <link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">
  <!--使用#{login.tip}实现国际化-->
  <h1 class="h3" th:text="#{login.tip}"></h1>
  
  
  <!--使用th:fragment="top"注册模板-->
  <nav class="navbar" th:fragment="top">
  <!--使用th:replace或th:insert引用模板-->  
  <div th:replace="~{/commons/commons::sidebar}"/>
    
  <!--注册模板时传递参数-->
  <a th:class="${active=='dashboard'?'nav-link ac':'nav-link'}"/>  
  <div th:replace="~{/commons/commons::sidebar(active='dashboard')}"/>
  
    
  <!--三元表达式-->  
  <td th:text="${emp.getGender()==0?'女':'男'}"/>
  <!--日期格式化-->
  <td th:text="${#dates.format(emp.getBirth(),'yyyy-mm-dd HH:mm:ss')}"/>  
  ```

### 装配扩展SpringMVC

- 实现自己的@Configuration类,要实现WebMvcConfigurer,之后重写特定的方法来实现对mvc的自定义

### 拦截器

- 自己实现拦截器实现HandlerInterceptor

- 在MVC配置类中配置自己的拦截器

  ```java
  //自己实现拦截器实现HandlerInterceptor
  //在MVC配置类中配置自己的拦截器
  public class LoginInterceptor implements HandlerInterceptor {
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          Object UserInfo = request.getSession().getAttribute("UserInfo");
          if (UserInfo==null){
              request.setAttribute("msg","请登录");
              request.getRequestDispatcher("/index").forward(request,response);
              return false;
          }
          return true;
      }
  }
  //-------------------------
  @Configuration
  public class MyMvcConfig implements WebMvcConfigurer {
      @Override
      public void addViewControllers(ViewControllerRegistry registry) {
         registry.addViewController("/main.html").setViewName("dashboard");
      }
  }  
  ```

### 国际化!

- 配置i18n文件

  ```yaml
  spring:
    messages:
      # 国际化文件位置
      basename: i18n.login
  ```

- 实现自定义国际化环境的切换

  - 自己实现LocaleResolver
  - 在WebMvcConfigurer中注入自己实现的LocaleResolver

  ```java
  //自己实现LocaleResolver
  //在MVC配置类中注入自己实现的LocaleResolver
  public class LocaleConfig implements LocaleResolver {
      @Override
      public Locale resolveLocale(HttpServletRequest request) {
          String l = request.getParameter("l");
          if (StringUtils.isNotEmpty(l)){
              try {
                  String[] s = l.split("_");
                  return new Locale(s[0],s[1]);
              } catch (Exception e) {
                  e.printStackTrace();
              }
          }
          return Locale.getDefault();
      }
  }
  //------------------
  @Configuration
  public class MyConfig implements WebMvcConfigurer {
      @Bean
      public LocaleResolver localeResolver(){
          return new LocaleConfig();
      }
  }
  ```

### 整合mybatis

- 导包

  ```xml
  <dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
  </dependency>
  ```

- 配置

  ```yaml
  spring:
    datasource:
      username: root
      password: root
      url: jdbc:mysql://10.0.0.2:3306/mybatis?useUnicode=true&characterEncoding=UTF-8
      driver-class-name: com.mysql.jdbc.Driver
  # 整合mybatis配置
  mybatis:
    # 别名配置
    type-aliases-package: com.sowevo.model
    # mapper扫描
    mapper-locations: classpath:mybatis/mapper/*.xml
  ```

- Mapper编写

  ```java
  @Mapper
  @Repository
  public interface UserMapper {
      List<User> queryUserList();
      User queryUserById(Integer id);
      int addUser(User user);
      int updateuser(User user);
      int deleteUser(Integer id);
  }
  ```

- Controller调用

  ```java
  @RestController
  @RequestMapping("/user")
  public class UserController {
      @Autowired
      UserMapper userMapper;
      @GetMapping("/")
      public List<User> getUser(){
          List<User> users = userMapper.queryUserList();
          return users;
      }
  }  
  ```

### SpringSecurity(安全!)

- 引入依赖

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
  ```

- 配置类

  ```java
  @EnableWebSecurity
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
      //授权
      @Override
      protected void configure(HttpSecurity http) throws Exception {
          //首页所有人可以访问,功能页要有权限才能访问
          //请求授权的规则
          http.authorizeRequests()
                  .antMatchers("/").permitAll()
                  .antMatchers("/level1/**").hasRole("vip1")
                  .antMatchers("/level2/**").hasRole("vip2")
                  .antMatchers("/level3/**").hasRole("vip3");
          //开启登录功能,没有权限会跳转到登录页
          http.formLogin()
                  //指定自己的登录页
                  .loginPage("/toLogin")
                  //指定登录的参数名
                  .usernameParameter("user").passwordParameter("pwd")
                  //登录处理链接
                  .loginProcessingUrl("/login");
          // 开启记住我功能
          http.rememberMe()
                  //指定记住我的选择框的名字
                  .rememberMeParameter("remember");
          //为了避免csrf,一些请求限制只能使用post请求,导致登出无法进行
          http.csrf().disable();
          //开启登出功能
          http.logout()
                  .logoutUrl("/logout")
                  //登出成功后跳转到哪里
                  .logoutSuccessUrl("/");
      }
      //认证
      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception {
          //假的用户信息,内存的
          auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                  .withUser("sowevo").password(new BCryptPasswordEncoder().encode("1")).roles("vip3","vip2").and()
                  .withUser("root").password(new BCryptPasswordEncoder().encode("root")).roles("vip1","vip2","vip3").and()
                  .withUser("guest").password(new BCryptPasswordEncoder().encode("1")).roles("vip1");
      }
  }
  ```

- 与Thymeleaf集成使用

  - 引入依赖

    ```xml
    <dependency>
      <groupId>org.thymeleaf.extras</groupId>
      <artifactId>thymeleaf-extras-springsecurity5</artifactId>
      <version>3.0.4.RELEASE</version>
    </dependency>
    ```

  - html命名空间

    ```html
    <html xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
    ```

  - 使用标签

    ```html
    <!--判断有没有登录-->
    <div sec:authorize="!isAuthenticated()"><a>登录</a></div>
    <!--判断有没有相应的权限-->
    <th sec:authorize="hasRole('vip3')">Level 3</th>
    ```

### Shiro(还是安全!)

- 三个核心组件

  - **Subject：**当前用户

  - **SecurityManager：**管理所有Subject

  - **Realms：**本质上是一个特定的安全 DAO：它封装与数据源连接的细节

    ![img](https://up.sowevo.com/img/20201217203702.png)

### 任务相关

- 异步任务
  - 手动创建线程
  - Spring支持的
    - 异步任务method 上添加@Async注解
    - 启动类加上@EnableAsync注解

- 邮件发送

  - 导包

    ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-mail</artifactId>
    </dependency>
    ```

  - 配置

    ```yaml
    spring:
      mail:
        password: ydcrkuadsflsdfe
        username: i@sowevo.com
        host: smtp.qq.com
        properties:
          mail.smtp.ssl.enable: true
    ```

  - 发邮件

    ```java
    @Autowired
    JavaMailSenderImpl mail;
    @Test
    void contextLoads() {
      SimpleMailMessage msg = new SimpleMailMessage();
      msg.setSubject("小花~");
      msg.setText("今天天气不错呀!");;
      msg.setFrom("i@sowevo.com");
      msg.setTo("sowevo@gmail.com");
      mail.send(msg);
    }
    ```

- 定时任务

  - 启动类加上@EnableScheduling注解

  - 任务method 上添加@Scheduled注解

    ```java
    //3秒一次,如果3秒内任务没有结束,会在任务结束之后在等待3秒在执行
    @Scheduled(cron = "*/3 * * * * ?")  
    //上一次任务结束3秒后在执行下一次
    @Scheduled(fixedDelay=3000) 
    @Scheduled(fixedDelayString = "3000") 
    //上一次任务开始3秒后在执行下一次
    @Scheduled(fixedRate=3000) 
    @Scheduled(fixedDelayString = "3000")  
    //initialDelay项目启动10秒后开始执行任务
    @Scheduled(fixedRate=3000,initialDelay=10000) 
    //同一个job不能同时执行
    //有多个job时,要同时执行,需要修改默认的 scheduled池大小 spring.task.scheduling.pool.size=10
    ```

### 集成Redis

- SpringBoot2.x之后默认的redis驱动被替换成了lettuce
  - jedis采用的直连,多线程不安全,BIO模式
  - lettuce采用的netty,实例可以多线程共享,不存在线程不安全的问题,NIO模式

- 依赖

  ```xml
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  ```

- 配置

  ```yaml
  spring:
    redis:
      host: 10.0.0.2
      port: 6379
  ```

- 简单的使用

  ```java
  @Autowired
  RedisTemplate redisTemplate;
  @Test
  void contextLoads() {
    //redisTemplate 操作不同的数据类型,命令和api基本一致
    //redisTemplate.opsForValue() 字符串
    //redisTemplate.opsForList()  list
    //redisTemplate.opsForSet()
    //redisTemplate.opsForHash()
    //redisTemplate.opsForZSet()
  
    //获取redis的链接对象
    //RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
    //connection.flushDb();
    //connection.flushAll();
  
    redisTemplate.opsForValue().set("key","床前明月光");
    System.err.println(redisTemplate.opsForValue().get("key"));
  }
  ```

- 对象保存到Redis

  - 对象保存的时候要实现序列化Serializable
  - 一般model类都要实现序列化

- 最佳实践(一组工具类)

  <script src="https://gist.github.com/Sowevo/02b79b99dfc97852a457f338e17c439d.js"></script>

### 分布式+Dubbo+Zookeeper+SpringBoot

- Dubbo概述

  ![image-20201218193036943](https://up.sowevo.com/img/20201218193037.png)

  | 节点                 | 角色说明                                                     |
  | -------------------- | ------------------------------------------------------------ |
  | 服务提供者`Provider` | 暴露服务的服务提供方,启动时向注册中心注册自己提供的服务      |
  | 服务消费者`Consumer` | 调用远程服务的服务消费方,启动时向注册中心订阅自己所需的服务,从注册中心提供的提供者中选一台(基于软负载均衡)进行调用,如果失败,再选一个 |
  | 注册中心`Registry`   | 服务注册与发现的注册中心,提供提供者的信息给消费者            |
  | 监控中心`Monitor`    | 统计服务的调用次数和调用时间的监控中心                       |

  ##### 调用关系说明

  1. 服务容器负责启动，加载，运行服务提供者。
  2. 服务提供者在启动时，向注册中心注册自己提供的服务。
  3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
  4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
  5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
  6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

- Dubbo-admin服务监控

  - 克隆[项目](https://github.com/apache/dubbo-admin/tree/master)自己打包:注意要用master分支,记得修改配置
  - 启动打好的jar包

### 项目集成Dubbo

- 启动zookeeper服务

- 启动Dubbo-admin监控服务(非必选)

- 导入依赖(提供者与消费者均要导入此依赖)

  ```xml
  <dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.7.8</version>
  </dependency>
  <dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-dependencies-zookeeper</artifactId>
    <version>2.7.8</version>
    <type>pom</type>
  </dependency>
  ```

- 配置文件:注册中心与服务名等

  ```yaml
  # 提供者
  server:
    port: 8081
  dubbo:
    application:
      # 自己的名字
      name: provider-server
    registry:
      address: zookeeper://10.0.0.2:2181
    scan:
      # 服务的包扫描
      base-packages: com.sowevo.service
  ---
  # 消费者
  server:
    port: 8082
  dubbo:
    application:
      # 自己的名字
      name: consumer-service
    registry:
      address: zookeeper://10.0.0.2:2181
  ```

- 代码

  - 提供者

    ```java
    @Service
    @DubboService
    public class TicketServiceImpl implements TicketService{
        @Override
        public String getTicket() {
            String ticket = RandomUtil.randomString(8);
            System.err.println("生成票号====>"+ticket);
            return ticket;
        }
    }
    ```

  - 消费者

    ```java
    @Service
    public class UserService {
        @DubboReference //从提供者拷贝过来的接口
        TicketService ticketService;
        public String buyTicket(){
            String ticket = ticketService.getTicket();
            System.err.println("获得票号====>"+ticket);
            return ticket;
        }
    }
    ```

## SpringBoot新的注解

- @ConfigurationProperties:将配置文件中的属性,映射到这个组件中,需要首先是组件(@Component),要配置参数`prefix`
- @Value:注入值.可以使用${name}从配置文件中获取值
- @Validated:对字段进行校验,需要引入`spring-boot-starter-validation`的依赖,需要搭配具体的校验规则的注解
  - @Email
  - @Size(max, min)
  - ....
- @Async搭配@EnableAsync 启动异步任务的支持~

- @Mapper 整合mybatis时使用