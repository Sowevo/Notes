![](https://mybatis.org/images/mybatis-logo.png)

# [官方文档](https://mybatis.org/mybatis-3/zh/index.html)

# 问题

Maven中默认打包 resource下的资源文件。如果我们的mapper.xml文件不放在resource的目录下，则需要通过配置告知 Maven 让它把指定目录的配置文件也打包

```xml
<build>
  <resources>
    <resource>
      <directory>src/main/java</directory>
      <includes>
        <include>**/*.xml</include>
      </includes>
      <filtering>true</filtering>
    </resource>
  </resources>
</build>
```

# 处理like模糊查询的情况

- 使用mybatis的bind定义

  ```xml
  <select id="queryBookByName" resultType="books">
    <bind name="pattern" value="'%' + bookName+ '%'" />
    select * from books b where bookName like #{pattern}
  </select>
  ```

- 使用mysql的 CONCAT()函数

  ```xml
  <select id="queryBookByName" resultType="books">
    select * from books b where bookName like CONCAT('%',#{bookName},'%')
  </select>
  ```

- 直接传入带着%的参数

  ```xml
  <!--调用时传入参数拼上%
  return booksMapper.queryBookByName("%" + bookName + "%";);
  -->
  <select id="queryBookByName" resultType="books">
    select * from books b where bookName like #{bookName}
  </select>
  ```

# sql中#{}与${}的区别

- 能用#{}就用#{}
  - #{}使用的是JDBC预编译
  - 拼接表名时要用${}
  - **${}存在sql注入的问题,尽量不要用,能用#{}就用#{}**

