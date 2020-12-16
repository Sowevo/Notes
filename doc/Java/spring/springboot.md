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

- 要解决的问题

  - 静态资源

    - 是否有自定义配置:`spring.mvc.static-path-pattern`,有的话按照自定义的来

    - 请求的路由是不是webjars的资源,是的话访问webjars的资源(不好用)

    - 最后按照如下顺序进行访问
      
      classpath:/META-INF/resources/
      classpath:/resources/
      classpath:/static/
      classpath:/public/

  - 首页

    - 从前面的静态资源目录中寻找index.html文件

  - 模板引擎Thymeleaf

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

  - 装配扩展SpringMVC

    - 实现自己的@Configuration类,要实现WebMvcConfigurer,之后重写特定的方法来实现对mvc的自定义

  - 增删改查

  - 拦截器

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

      

  - 国际化!

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

      

## SpringBoot新的注解

- @ConfigurationProperties:将配置文件中的属性,映射到这个组件中,需要首先是组件(@Component),要配置参数`prefix`
- @Value:注入值.可以使用${name}从配置文件中获取值
- @Validated:对字段进行校验,需要引入`spring-boot-starter-validation`的依赖,需要搭配具体的校验规则的注解
  - @Email
  - @Size(max, min)
  - ....