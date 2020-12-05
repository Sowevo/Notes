# [Arthas阿尔萨斯](https://alibaba.github.io/arthas/)

![](https://alibaba.github.io/arthas/_images/arthas.png)

# 在线文档

- [教程](https://alibaba.github.io/arthas/arthas-tutorials?language=cn)
- [文档](https://alibaba.github.io/arthas/)

## 安装与启动 

### 在线安装

- 下载

  ```shell
  $ wget --no-check-certificate  https://alibaba.github.io/arthas/arthas-boot.jar
  # github比较慢的话可以使用
  $ wget --no-check-certificate https://arthas.gitee.io/arthas-boot.jar
  ```

- 启动

  ```shell
  $ java -jar arthas-boot.jar
  # 如果下载速度比较慢，可以使用aliyun的镜像
  $ java -jar arthas-boot.jar --repo-mirror aliyun --use-http
  ```

## JSON格式查看结果

```shell
# json-format 是否支持json化的输出,默认false
$ options json-format true
```




# 常用命令

## [trace](https://alibaba.github.io/arthas/trace.html) 分析性能

方法内部调用路径，并输出方法路径上的每个节点上耗时

- 使用例子

  - 基本使用
    ```shell
    # trace 类名 方法名
    $ trace bp.ScheduleBP queryWebSchedule
    ```
    
  - 过滤掉jdk的函数
    ```shell
    # -j 过滤掉jdk的函数耗时
    $ trace -j bp.ScheduleBP queryWebSchedule
    ```
    
  - 根据耗时过滤
    ```shell
    # 只会展示耗时大于10ms的调用路径
    $ trace bp.ScheduleBP queryWebSchedule '#cost > 10'
    ```
  
  - 限制执行次数
  
    ```shell
    # 监控10次后退出
    $ trace bp.ScheduleBP queryWebSchedule -n 10
    ```
  
  - trace多个类或者多个函数
  
    ```shell
    # 可以用正则表匹配路径上的多个类和函数，一定程度上达到多层trace的效果。
    $ trace -E com.test.ClassA|org.test.ClassB method1|method2|method3
    ```

##　[watch](https://alibaba.github.io/arthas/watch.html) 分析入参出参等

方法执行数据观测,查看方法入参,出参,更建议使用tt

| 参数名称            | 参数说明                                   |
| ------------------- | ------------------------------------------ |
| *class-pattern*     | 类名表达式匹配                             |
| *method-pattern*    | 方法名表达式匹配                           |
| *express*           | 观察表达式                                 |
| *condition-express* | 条件表达式                                 |
| [b]                 | 在**方法调用之前**观察                     |
| [e]                 | 在**方法异常之后**观察                     |
| [s]                 | 在**方法返回之后**观察                     |
| [f]                 | 在**方法结束之后**(正常返回和异常返回)观察 |
| [E]                 | 开启正则表达式匹配，默认为通配符匹配       |
| [x]                 | 指定输出结果的属性遍历深度，默认为 1       |

- 使用例子

  - 观察方法出参和返回值

    ```shell
    $ watch bp.ScheduleBP queryWebSchedule "{params,returnObj}" -x 2
    ```

  - 观察方法入参

    ```shell
    # 事件点为方法执行前，因此获取不到返回值
    $ watch bp.ScheduleBP queryWebSchedule "{params,returnObj}" -x 2 -b
    ```

  - 同时观察方法调用前和方法返回后

    ```shell
    # 参数里-n 2，表示只执行两次
    # 第一次输出的是方法调用前的观察表达式的结果，第二次输出的是方法返回后的表达式的结果
    # 结果的输出顺序和事件发生的先后顺序一致，和命令中 -s -b 的顺序无关
    $ watch bp.ScheduleBP queryWebSchedule "{params,target,returnObj}" -x 2 -b -s -n 2
    ```

  - -x含义:遍历深度

    ```shell
    # -x表示遍历深度，可以调整来打印具体的参数和结果内容，默认值是1
    $ watch bp.ScheduleBP queryWebSchedule "{params,returnObj}" -x 1
    $ watch bp.ScheduleBP queryWebSchedule "{params,returnObj}" -x 2
    ```

  - 观察当前对象中的属性

    ```shell
    # 当前对象中的属性，可以使用target关键字，代表当前对象
    $ watch bp.ScheduleBP queryWebSchedule 'target'
    # 然后使用target.field_name访问当前对象的某个属性
    $ watch bp.ScheduleBP queryWebSchedule 'target.cacheMap'
    ```

# [profiler](https://alibaba.github.io/arthas/profiler.html)

使用[async-profiler](https://github.com/jvm-profiling-tools/async-profiler)生成火焰图

```shell
# 启动profiler
$ profiler start
# 获取已采集的sample的数量
$ profiler getSamples
# 查看profiler状态
$ profiler status

# 停止profiler,默认生成svg格式
$ profiler stop
# 停止profiler,指定保存位置
$ profiler stop --file /tmp/output.svg
# 停止profiler,指定HTML格式
$ profiler stop --format html
```

默认情况下，生成的是cpu的火焰图，即event为`cpu`。可以用`--event`参数来指定。

# tt 批量记录入参出参等

记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测

| 参数名称          | 参数说明                                                 |
| ----------------- | -------------------------------------------------------- |
| -d                | 删除指定的记录,需要指定-i参数                            |
| --delete-all      | 删除全部记录                                             |
| -x                | 指定输出结果的属性遍历深度，默认为                       |
| -i                | 显示指定index的记录的详情                                |
| -n                | 记录次数限制                                             |
| -l                | 列出所有记录                                             |
| -p                | 重新执行一条记录(重发请求)                               |
| --replay-interval | 搭配-p,重发间隔                                          |
| --replay-times    | 搭配-p,重发次数                                          |
| -w                | ognl参数,例如params;params[0];params[0].size();returnObj |
| -t                | 记录方法调用                                             |

- 使用例子

  - 记录方法调用

    ```shell
    # 记录方法调用
    $ tt -t bp.ScheduleBP queryWebSchedule
    ```

  
  - 记录大于50ms的方法调用
  
    ```shell
    # 记录方法调用
    $ tt -t bp.ScheduleBP queryWebSchedule '#cost > 50'
    ```
  
   - 查看index为1004的记录的详情

     ```shell
     # 查看index为1004的记录的详情
     $ tt -i 1104
     ```
  
  - 查看index为1004的记录的第三个请求参数
  
    ```shell
    $ tt -i 1104 -w params[2]
    ```
  
  - 记录第一个请求参数长度小于3的
  
    ```shell
    $ tt -t utils.ScheduleUtil init 'params[0].size()<3'
    ```

# dashboard

####　数据说明

当前系统的实时数据面板，按 ctrl+c 退出。

- ID: Java级别的线程ID，注意这个ID不能跟jstack中的nativeID一一对应
- NAME: 线程名
- GROUP: 线程组名
- PRIORITY: 线程优先级, 1~10之间的数字，越大表示优先级越高
- STATE: 线程的状态
- CPU%: 线程消耗的cpu占比，采样100ms，将所有线程在这100ms内的cpu使用量求和，再算出每个线程的cpu使用占比。
- TIME: 线程运行总时间，数据格式为`分：秒`
- INTERRUPTED: 线程当前的中断位状态
- DAEMON: 是否是daemon线程

# thread

查看当前线程信息，查看线程的堆栈

| 参数名称   | 参数说明                              |
| ---------- | ------------------------------------- |
| *id*       | 线程id                                |
| [n:]       | 指定最忙的前N个线程并打印堆栈         |
| [b]        | 找出当前阻塞其他线程的线程            |
| [i<value>] | 指定cpu占比统计的采样间隔，单位为毫秒 |

# 基础命令

- [reset](https://alibaba.github.io/arthas/reset.html)
  - 重置增强类，将被 Arthas 增强过的类全部还原，Arthas 服务端关闭时会重置所有增强过的类
- stop/shutdown
  - 关闭 Arthas 服务端，所有 Arthas 客户端全部退出
- quit/exit
  - 退出当前 Arthas 客户端，其他 Arthas 客户端不受影响
  - 退出Arthas之后，还可以再次用 `java -jar arthas-boot.jar` 来连接。
  - `exit/quit`命令只是退出当前session，arthas server还在目标进程中运行。
  - 一定要记得彻底退出



