# MySql

## 存储引擎

- InnoDB:
  - 支持事务处理
  - 支持外键
  - 支持崩溃修复能力和并发控制
  - 支持聚集索引,主键和数据在一起存储
- MyISAM
  - 不支持事务
  - 不支持外键
  - 不支持聚集索引
  - 索引和数据分开存储

## 索引

- 普通索引

  - 普通索引:最基本的索引，它没有任何限制。
  - 组合索引:指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用组合索引时遵循最左前缀集合
  - 唯一索引:与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。

- 主键索引

  就是表的主键

- 全文索引

  主要用来查找文本中的关键字，而不是直接与索引中的值相比较。fulltext索引跟其它索引大不相同，它更像是一个搜索引擎，而不是简单的where语句的参数匹配。fulltext索引配合match against操作使用，而不是一般的where语句加like。

- 主键与普通唯一索引的区别
  - 主键会自动创建唯一索引
  - 主键可以作为外键,唯一索引不可以
  - 主键默认not null,每个表只能有一个
  - 主键和唯一索引都可以是多个字段的组合

- 索引失效的情况
  - 当语句使用or的时候,or的所有条件都要有索引,否则会失效。
  - 当语句索引like带%在左边时候索引失效
  - 索引与查询语句中类型不一致
  - 如果mysql估计使用全表扫描要比使用索引快
  - 联合索引顺序不对的情况
  - 对索引进行计算操作(数学计算,函数计算等)
  - 查询使用了  不等于  not in 
- 索引的注意事项
  - 索引键的大小不能超过900个字节,当超过900个字节时会报错
  - 对于内容基本重复的列，比如只有1和0，禁止建立索引
  - 当一个索引有多个列构成时，应将选择性强的列放在前面。
  - 索引会影响插入修改性能,索引不是越多越好

## 索引的数据结构

- B tree与B+Tree

  - B Tree
    1. 所有键值分布在整棵树中
    2. 搜索可能在非叶子节点结束
    3. 所有叶子节点都在同一层
    4. 数据分布在整棵树中

  ![索引](https://up.sowevo.com/img/20201230153826.png)

- B+Tree

  1. B+Tree是对B Tree的一种优化
  2. 非叶子节点存储key,叶子节点存储key和value
  3. 每个节点可以存储多的节点信息,降低树的高度
  4. 叶子节点之间相互连接,便于范围查询

  ![索引](https://up.sowevo.com/img/20201230153959.png)

- 区别

  - 数据的存储方式不同,B+ Tree存储在叶子节点,B Tree存储在整个树中
  - 非叶子节点中可以存储更多的信息
  - 叶子节点之间相互连接,便于范围查询

## 聚簇索引 & 非聚簇索引

聚集索引与非聚集索引的区别是：叶节点是否存放一整行记录

InnoDB 主键使用的是聚簇索引,MyISAM 不管是主键索引，还是二级索引使用的都是非聚簇索引

- 聚簇索引的优点
  1. 范围查询的时候性能比非聚集索引高
  2. 查询速度比非聚集索引高
  3. 覆盖索引可以直接用节点上的值
- 聚簇索引的缺点
  1. 主键生成无序会导致插入性能问题,一页放不下了,变成两页
  2. 更新主键会导致性能问题,数据从这一页移动到另一页
  3. 普通索引非覆盖的情况下进行查询,会造成回表,**第一次找到主键值,第二次根据主键值找到行数据**
  4. 整体插入性能下降:判断主键唯一时要查更多的数据

## 啥是回表

普通索引进行查询时没有获取到所需要的所有字段,需要根据id再查一遍的情况

解决方法:使用覆盖索引,要查询的字段建立成组合索引

## 隔离级别

### 级别低到高排列

- 读未提交(Read Uncommitted)
  - 当前事务可以读到其他事物未提交的数据
- 读已提交(Read Committed)
  - 当前事务可以读到其他事物已提交的数据,并且多次读取数据可能会不一致
- 可重复读(Repeatable Read)
  - 当前事务可以读到其他事物已提交的数据,并且多次读取数据一致
  - mysql默认使用
- 可串行化(Serializable)
  - 当前事物开启后,其他事物无法进行写操作,需要进行等待

### 解决的一些问题

- 脏读(Dirty Read)
  - 一个事务读到了另一个事务未提交的数据
- 不可重复读(Non-Repeatable Read)
  - 一个事物两次读取结果不一致
- 幻读(Phantom)
  - 一个事物读取不到另一个事物提交的数据,但是能更新这条数据,并且更新后能读到

### 表格

|                            | 脏读 | 不可重复读 | 幻读 |
| :------------------------: | :--: | :--------: | :--: |
| 读未提交(Read Uncommitted) | 可能 |    可能    | 可能 |
|  读已提交(Read Committed)  | 不会 |    可能    | 可能 |
| 可重复读(Repeatable Read)  | 不会 |    不会    | 可能 |
|   可串行化(Serializable)   | 不会 |    不会    | 不会 |

## MySQL的多版本并发控制(MVCC)

使用MySQL的默认隔离级别（可重复读）来进行说明

基于回滚操作日志(undo log)实现

- 版本链:两个隐藏字段,记录当前操作的事物id与上一个版本的undo log地址
- 快照:记录了
  - 当前事务id(creator_trx_id)
  - 最小的活跃事物id(min_trx_id)
  - 下一个事物id(max_trx_id)
  - 所有的活跃id(m_ids)

- 查询数据时
  - 当查询的数据id等于当前事物id,可见
  - 当查询的数据id小与最小活跃id,可见
  - 当查询的数据id大于下一个事物id,不可见
  - 当查询的数据id介于最小id与下一个id之间时
    - 等于活跃id的不可见
    - 不等于活跃id的可见
  - 从版本链中最新的数据开始遍历,直到找到一个可见的返回
  - 如果是隔离级别是读已提交,则当前事物中每次查询都会重新生成快照

## 事物的四个特性AIDC

- **原子性（Atomicity）**
  - 要么全部成功，要么全部失败回滚,不可分割
- **隔离性（Isolation）**
  - 多个事物之间不存在互相干扰的情况
- **持久性（Durability）**
  - 一个事务一旦被提交了，对数据库中的数据的改变就是永久性的
- **一致性（Consistency）**
  - 在事务开始之前和事务结束以后,数据库整体都是一致的

















----





# 相关脚本

### 授权所有ip远程连接mysql并刷新权限

```mysql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'IDENTIFIED BY 'sora' WITH GRANT OPTION;
flush privileges;
```

### 授权某个ip远程连接mysql并刷新权限

```mysql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.1.1'IDENTIFIED BY 'sora' WITH GRANT OPTION;
flush privileges;
```

### 关联更新例子

```mysql
UPDATE stationinfo si
LEFT JOIN searchstationrelation se on si.id = se.id
LEFT JOIN normstation ns ON si.normstationid = ns.id 
SET 
si.city_jianpin = NULL,
si.city_quanpin = NULL,
si.stationflag = ns.stationflag,
si.city = CASE WHEN ns.stationflag = 0 THEN ns.cityname WHEN ns.stationflag = 1 THEN ns.countyname ELSE ns.towns END,
si.buscityid = CASE ns.stationflag WHEN 0 THEN ns.citycode WHEN 1 THEN ns.countycode ELSE ns.townsid END,
se.city_jianpin = NULL,
se.city_quanpin = NULL,
se.stationflag = ns.stationflag,
se.cityname = CASE WHEN ns.stationflag = 0 THEN ns.cityname WHEN ns.stationflag = 1 THEN ns.countyname ELSE ns.towns END,
se.buscityid = CASE ns.stationflag WHEN 0 THEN ns.citycode WHEN 1 THEN ns.countycode ELSE ns.townsid END
WHERE si.normstationid IS NOT NULL AND si.stationflag <> ns.stationflag;
```

