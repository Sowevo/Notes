# SpringMVC

## 1.两种配置方式

### 1.1 完全使用配置文件

  - web.xml注册dispatcherservlet

    ```xml
    <!--配置dispatcherservlet-->
    <servlet>
      <servlet-name>dispatcherservlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <!--绑定Spring的配置文件路径-->
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mvc.xml</param-value>
      </init-param>
      <!--启动级别 1:服务器启动时启动-->
      <load-on-startup>1</load-on-startup>
    </servlet>
    <!--配置dispatcherservlet路由-->
    <servlet-mapping>
      <servlet-name>dispatcherservlet</servlet-name>
      <!--
            /和/*的区别
            /:只匹配请求,不匹配jsp
            /*:匹配所有,包含jsp,会导致重定向过多
            -->
      <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```

- Spring配置

  - 处理器映射器
  - 处理器适配器

  - 视图解析器

  ```xml
  <!--处理器映射器-->
  <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
  <!--处理器适配器-->
  <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
  <!--视图解析器:模板引擎-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
    <property name="suffix" value=".jsp"/>
    <property name="prefix" value="/WEB-INF/jsp/"/>
  </bean>
  
  <!--因为使用的是BeanNameUrlHandlerMapping映射器,所以用Bean管理Controller(要实现Controller)-->
  <bean id="/hello" class="com.sowevo.controller.HelloController"/>
  ```

### 1.2. 使用注解

- web.xml注册dispatcherservlet

  ```xml
  <!--配置dispatcherservlet-->
  <servlet>
    <servlet-name>dispatcherservlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--绑定Spring的配置文件路径-->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <!--启动级别 1:服务器启动时启动-->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <!--配置dispatcherservlet路由-->
  <servlet-mapping>
    <servlet-name>dispatcherservlet</servlet-name>
    <!--
            /和/*的区别
            /:只匹配请求,不匹配jsp
            /*:匹配所有,包含jsp,会导致重定向过多
            -->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```

- Spring配置

  ```xml
  <!--开启包扫描,使注解生效-->
  <context:component-scan base-package="com.sowevo.controller"/>
  
  <!--静态资源的过滤处理-->
  <mvc:default-servlet-handler/>
  <!--mvc的注解启动!-->
  <mvc:annotation-driven/>
  <!--视图解析器-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
  </bean>
  ```

- Controller的编写

  ```java
  @Controller
  @RequestMapping("/hello")
  public class HelloController {
      @RequestMapping("/h1")
      public String hello(Model model){
          //封装数据
          model.addAttribute("msg","Hello SpringMVC Annotation");
          //被视图解析器处理 hello.jsp
          return "hello";
      }
  }
  ```

### 1.3 SpringMVC配置的三个重点配置

- 处理器映射器
- 处理器适配器
- 视图解析器
- 通常,我们只需要配置视图解析器,而处理器映射器和处理器适配器只需要开启注解驱动即可搭配注解使用

## 2.RestFul风格的支持

- 相关注解
  - @PathVariable:为参数加上注解实现路径与参数的转换
  - @GetMapping,@PutMapping,@DeleteMapping等注解实现http的method匹配
    - 等价于@RequestMapping加上一个参数:method = RequestMethod.DELETE实现切换

## 3. 没有视图解析器的情况

```java
@Controller
public class ResultController {
    @RequestMapping("/r/t1")
    public String test1(Model model){
        model.addAttribute("msg","转发1");
        //没有视图解析器
        return "/WEB-INF/jsp/test.jsp";
    }
    @RequestMapping("/r/t2")
    public String test2(Model model){
        model.addAttribute("msg","转发2");
        //没有视图解析器
        return "forward:/WEB-INF/jsp/test.jsp";
    }
    @RequestMapping("/r/t3")
    public String test3(Model model){
        model.addAttribute("msg","重定向");
        return "redirect:/index.jsp";
    }
}
```

## 4. 参数的传递

- 前端传过来参数与Controller中方法的参数名称一致

  - 可以直接匹配,但是建议加上@RequestParam

- 前端传过来参数与Controller中方法的参数名称不一致

  - 必须使用@RequestParam进行匹配

- 前端传过来是个model的情况

  - 如果前端传递的参数名与model的参数名一致,可以使用
    如果前端传递的参数名与model不一致,则匹配不到,值为null

## 5. 乱码的处理

- 使用Spring提供的过滤器处理,xml中增加过滤器配置

  ```xml
  <!--Spring提供的过滤器处理乱码-->
  <filter>
      <filter-name>encoding</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>utf-8</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>encoding</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

## 6. Spring中使用JSON

- 导入fastjson包

- 注解修改,不走视图解析器,直接返回数据到前端(选一个)

  - @Controller+@ResponseBody
  - @RestController注解

- Spring配置中加上全局配置,进行message转换的相关配置

  ```xml
  <mvc:annotation-driven>
    <mvc:message-converters register-defaults="true"><!--除了此元素提供的注册之外，是否还应添加默认的HttpMessageConverter注册-->
      <!--配置返回前端String的MediaType的编码-->
      <bean class="org.springframework.http.converter.StringHttpMessageConverter">
        <constructor-arg value="UTF-8"/>
      </bean>
      <!--jackson的一些配置-->
      <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
        <property name="objectMapper">
          <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
            <!--对象中有空值的处理.默认为true,会报错无法转换-->
            <property name="failOnEmptyBeans" value="false"/>
          </bean>
        </property>
      </bean>
    </mvc:message-converters>
  </mvc:annotation-driven>
  ```

# 7. <font color=red>拦截器与过滤器的不同</font>

- 不同点
  - 拦截器(Interceptor)是Spring的,过滤器 (Filter)是Servlet的
  - 拦截器(Interceptor)基于反射,过滤器 (Filter)基于函数回调
  - 拦截器(Interceptor)是Spring的,Spring的各种地方都可以使用,过滤器 (Filter)是Servlet的,只能在web中使用
  - 拦截器(Interceptor)在Servlet之后执行,过滤器 (Filter)在Servlet之前执行
  - 拦截器(Interceptor)中可以使用Spring的相关内容(bean等),过滤器 (Filter)中则不可以
  - 多个拦截器(Interceptor)的顺序可以由@Order控制,多个过滤器 (Filter)由注册的顺序控制
  - Spring中推荐使用拦截器(Interceptor)

# 8. 转发与重定向

- 转发是服务内部转发
- 重定向是浏览器重定向