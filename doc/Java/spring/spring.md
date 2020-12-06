# 1. 控制反转

 - 控制反转（Inversion of Control，缩写为IoC）
 - 控制反转是一种通过描述(xml或注解)并通过第三方去生产或获取特定对象的方式.在Spring中实现控制反转的是IoC容器,其实现方式是依赖注入

# 2. 配置

- bean

  - ```xml
    <!--
    id:bean的唯一标识符
    class:bean所对应的类的全限定名:包名+类名
    name:别名,并且name可以使用逗号,空格,分号等 分割多个别名,比alias标签更好用
    -->
    <bean id="user" class="com.sowevo.model.User" name="我也是别名,我还是别名">
      <property name="name" value="小张"/>
    </bean>
    ```

- alias(别名)

  - ```xml
    <!--别名,若用了别名,我们可以通过别名获取这个对象bean-->
    <alias name="user" alias="我是一个别名"/>
    ```

- import

  - 导入其他的配置文件,多个xml内容合并到同一个里面

  - ```xml
    <import resource="beans.xml"/>
    ```

# 3. 依赖注入

## 3.1 构造器注入

- 在类中，不用为属性设置setter方法，但是需要生成该类带参的构造方法。
- 在配置文件中配置该类的bean，并配置构造器，在配置构造器中用到了<constructor-arg>节点
  - 通下标创建                 <constructor-arg index="0" value="Hello"/>
  - 通过类型创建,不推荐 <constructor-arg type="java.lang.String" value="Hello"/>
  - 通过参数名构造          <constructor-arg name="name" value="Hello" />

## 3.2 Set方式注入

```xml
<bean id="student" class="com.sowevo.model.Student">
  <!--1.普通值注入,value-->
  <property name="name" value="小花"/>
  <!--2.Bean注入,ref-->
  <property name="address" ref="address"/>
  <!--3.数组注入,array-->
  <property name="books">
    <array>
      <value>MySql从入门到精通</value>
      <value>MySql从入门到删库</value>
      <value>MySql从入门到跑路</value>
    </array>
  </property>
  <!--4.List集合注入,list-->
  <property name="hobbys">
    <list>
      <value>唱</value>
      <value>跳</value>
      <value>Rap</value>
    </list>
  </property>
  <!--5.Map注入,map-->
  <property name="cards">
    <map>
      <entry key="银行卡" value="610000000000000000"/>
      <entry key="身份证" value="510000000000000000"/>
    </map>
  </property>
  <!--6.Set集合注入,set-->
  <property name="games">
    <set>
      <value>LOL</value>
      <value>WOW</value>
      <value>BOB</value>
    </set>
  </property>
  <!--7.空值注入,null-->
  <property name="wife">
    <null/>
  </property>
  <!--8.属性注入,props-->
  <property name="info">
    <props>
      <prop key="姓名">小花</prop>
      <prop key="地址">邢台市襄都区丰仓街东150米</prop>
      <prop key="学号">110101010203</prop>
      <prop key="性别">男</prop>
    </props>
  </property>
</bean>
```

## 3.3 拓展方式注入

我们可以使用p命名空间与c命名空间进行注入

# 4. bean的作用域

- bean的scope参数
  - 默认为singleton,单例模式
  - prototype,每次从容器中get的都是新对象
  - request   web中用的
  - session    web中用的
  - application    web中用的
  - websocket     web中用的

# 5. bean的自动装配

- 自动装配是spring满足bean依赖的一种方式

- Spring中有三种装配方式

  - xml中配置

  - Java中显示配置

  - 隐式的自动装配[重要!]

    - byName    自动在容器中查找和自己对象的set方法对应的beanid,需保证容器中beanid唯一

      ```xml
      <bean id="cat" class="com.sowevo.model.Cat"/>
      <bean id="dog" class="com.sowevo.model.Dog"/>
      <bean id="people" class="com.sowevo.model.People" autowire="byName">
        <property name="name" value="小花"/>
      </bean>
      ```

    - byType   自动在容器中查找和自己对象类型一致的bean,需保证容器中bean类型唯一

      ```xml
      <bean id="cat" class="com.sowevo.model.Cat"/>
      <bean id="dog" class="com.sowevo.model.Dog"/>
      <bean id="people" class="com.sowevo.model.People" autowire="byType">
        <property name="name" value="小花"/>
      </bean>
      ```

- 使用注解进行装配

  1. 配置

     1.1 导入xml约束

        ```
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="
                http://www.springframework.org/schema/context
                http://www.springframework.org/schema/context/spring-context.xsd">
        ```
     1.2 配置注解的支持

     ```xml
   <context:annotation-config/>
     ```
  
  2. @Autowired 
  
   2.1 可以加到属性上,也可以加到方法对应的Set方法上
     2.2 使用@Autowired可以不写Set方法
   2.3 @Autowired(required = false)  required属性为false时,找不到bean不报错
     2.4 @Autowired先使用ByType查找,如果存在重复type,可以搭配@Qualifier(value='xxx')ByName过滤
  
  3. @Resource

     3.1 可以加到属性上,也可以加到方法对应的Set方法上
   3.2 使用@Resource可以不写Set方法
     3.3 默认按 byName自动注入

  4. @Autowired与@Resource的差异
  
   4.1. 相同
  
       - 可以加到属性上,也可以加到方法对应的Set方法上
       - 可以不写Set方法
  
     5.1 不同
  
     - 顺序不同
     
       @Autowired默认ByType,搭配@Qualifier(value='xxx')可以实现ByName
     
       @Resource默认ByName,名称可以通过name属性进行指定,没有name时使用字段名,ByName找不到时ByType

# 6. 使用注解开发

- 配置

  - 导入xml约束

    ```
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
               http://www.springframework.org/schema/context
               http://www.springframework.org/schema/context/spring-context.xsd">
    ```

  - 配置注解的支持

    ```xml
    <context:annotation-config/>
    ```

  - 组件扫描的配置

    ```xml
    <!--使用@Component一系列注解时时需要开启扫描-->
    <context:component-scan base-package="com.sowevo"/>
    ```

- @Autowired  上面👆

- @Resource  上面👆

- @Qualifier 上面👆

- @Component 组件
  - 放在类上,说明这个类已经被Spring管理了!
  - 衍生的几个注解,在web开发中按照mvc进行分层
    - Dao层👉@Repository
    
    - Service层👉@Service
    
    - Controller层👉@Controller
    
      功能与@Component完全一致
  
- @Scope  作用域

  - 默认为singleton,单例模式
  - prototype,每次从容器中get的都是新对象
  - request   web中用的
  - session    web中用的
  - application    web中用的
  - websocket     web中用的

- 总结:

  - xml更万能,适于任何场景!维护更加方便
  - 注解不是自己的类使用不了,维护相对复杂
  - 最佳实践:xml管理bean,注解负责属性注入

# 7. 完全使用Java配置Spring

- **SpringBoot**中大量使用纯配置的方式

- @Configuration 代表这是一个配置类,就如同applicationContext.xml,这个类也会被Spring托管,因为他有@Component注解

- @Import 多个配置类的导入,类似于applicationContext.xml中的import标签

- @Bean 注册一个Bean,相当于applicationContext.xml中的bean标签,方法名就是bean标签id,返回值就是bean标签的class

- @ComponentScan("com.sowevo.model")   指定组件扫描的包范围,等价于applicationContext.xml中的context:component-scan标签

- 例子

  ```java
  //配置
  @Configuration
  @Import(OtherConfig.class)
  public class SowevoConfig {
      @Bean
      public User getUser(){
          return new User();
      }
  }
  //获取
  @Test
  public void test(){
      ApplicationContext context = new AnnotationConfigApplicationContext(SowevoConfig.class);
      User user = context.getBean("getUser", User.class);
      System.err.println(user);
  }
  ```

# 8. 代理模式

为什么要知道代理模式,因为这是SpringAOP的底层

### 分类

- 静态代理

  - 角色
    - 抽象角色:接口或抽象类
    - 真实角色:被代理的角色
    - 代理角色:代理真实角色的角色
    - 客户角色:访问代理对象的角色
  - 优势与劣势
    - 优势
      - 可以是真实角色的操作更加纯粹,不用关注公共业务
      - 公共业务交给代理角色,实现了业务的分工
      - 公共业务发生拓展的时候,更方便集中管理
    - 劣势
      - 一个真实角色就会产生一个代理角色;代码量会翻倍,效率低

- 动态代理

  - 角色

    - 抽象角色:接口或抽象类
    - 真实角色:被代理的角色
    - 代理角色:代理真实角色的角色
    - 客户角色:访问代理对象的角色
    - 和静态代理一样,动态代理的代理角色是动态生成的

  - 几种类型

    - 基于接口的动态代理-JDK的动态代理[需要会用的]
    - 基于类的动态代理-cglib
    - Java字节码实现-javasist

  - JDK的动态代理

    - 使用java.lang.reflect.Proxy和java.lang.reflect.InvocationHandler

      ```java
      // 动态代理类
      public class ProxyInvocationHandler implements InvocationHandler {
          //被代理的接口
          private Object target;
      
          public void setTarget(Object target) {
              this.target = target;
          }
          //得到代理类
          public Object getProxy(){
              return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
          }
          //处理代理实例并返回结果
          @Override
          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
              log(method.getName());
              Object o = method.invoke(target, args);
              return o;
          }
      
          public void log(String method){
              System.err.println("执行了"+method+"方法");
          }
      }
      //调用
      public static void main(String[] args) {
        //真实角色
        UserServiceImpl userServiceImpl = new UserServiceImpl();
        //代理角色,不存在
        ProxyInvocationHandler proxy = new ProxyInvocationHandler();
        //设置代理类
        proxy.setTarget(userServiceImpl);
        //动态生成代理类
        UserService u = (UserService) proxy.getProxy();
      
      
        u.add();
        u.delete();
        u.query();
      }
      ```

  - 优势

    - 可以是真实角色的操作更加纯粹,不用关注公共业务
  - 公共业务交给代理角色,实现了业务的分工
    - 公共业务发生拓展的时候,更方便集中管理
    - 一个动态代理类代理的是一个接口,一般就是一类业务
    - 一个动态代理类可以代理多个类,只要实现了同一个接口

# 9.AOP

### 9.1 AOP在spring中的作用

提供声明式事务,允许用户自定义切面

- 横切关注点:跨应用多个模块的方法或功能,及时,与我们业务无关的,但是需要关注的部分,例如日志,缓存,事物等
- 切面(Aspect):横切关注点被模块化的对象,是一个类
- 通知(Advice):横切关注点要完成的工作,类中的一个方法
- 目标(Target):被通知对象  原对象
- 代理(Proxy):原对象执行通知后创建的代理对象
- 切入点(PointCut):切面通知执行的"地点"
- 连接点(JointPoint):与切入点匹配的执行点

### 9.2 使用Spring实现AOP

- 使用AOP需要导入一个依赖包

  ```xml
  <dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
  </dependency>
  ```

- 方式一:使用Spring的接口

  - 编写自己的通知(Advice)类,实现通知(Advice)的接口,并交给Spring管理

    - AfterReturningAdvice,MethodBeforeAdvice等接口...

  - xml中配置

    ```xml
    <bean id="afterlog" class="com.sowevo.log.AfterReturnLog"/><!--通知(Advice)类-->
    <bean id="beforelog" class="com.sowevo.log.BeforeLog"/>    <!--通知(Advice)类-->
    <aop:config>
      <!--execution 要执行的位置!-->
      <aop:pointcut id="point" expression="execution(* com.sowevo.service.UserService.*(..))"/>
      <aop:advisor advice-ref="afterlog" pointcut-ref="point"/>
      <aop:advisor advice-ref="beforelog" pointcut-ref="point"/>
    </aop:config>
    ```

- 方式二:自定义来实现AOP

  - 编写自定义的通知类,并交给Spring管理

  - xml中配置

    ```xml
    <bean id="diy" class="com.sowevo.diy.DiyPointCut"/>
    <aop:config>
      <!--自定义切面-->
      <aop:aspect ref="diy">
        <!--切入点-->
        <aop:pointcut id="point" expression="execution(* com.sowevo.service.UserService.*(..))"/>
        <!--通知-->
        <aop:after method="after" pointcut-ref="point"/>
        <aop:before method="before" pointcut-ref="point"/>
      </aop:aspect>
    </aop:config>
    ```

- 方式三:使用注解实现

  - 编写自定义的通知类,并交给Spring管理

    ```java
    @Aspect
    public class AnnottationPointCut{
        @Before("execution(* com.sowevo.service.UserService.*(..))")
        public void before(){
            System.err.println("方法执行前~~");
        }
        @After("execution(* com.sowevo.service.UserService.*(..))")
        public void after(){
            System.err.println("方法执行后~~");
        }
        @Around("execution(* com.sowevo.service.UserService.*(..))")
        public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
            System.err.println("方法环绕前~~");
            proceedingJoinPoint.proceed();
            System.err.println("方法环绕后~~");
        }
    }
    ```

  - xml中配置开启AOP注解支持

    ```xml
    <bean id="annoPoint" class="com.sowevo.diy.AnnottationPointCut"/>
    <aop:aspectj-autoproxy/>
    ```

    



