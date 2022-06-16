# Recommendation-system-study-notes
Recommendation system study notes

[01 推荐系统](#1)
[02 大数据框架](#2)
[03 SQL](#3)

<span id="1"> </span>
# 推荐系统

#### 基于相似度的协同过滤推荐

#### 基于回归模型的协同过滤推荐

+ baseline 
  + 平均值加上用户偏差和物品偏差
+ LFM(latent factor model)隐语义模型
  + 基于矩阵分解的CF算法
  + SVD奇异值分解
    + 一个大矩阵分成3个小矩阵 中间的是一个k方阵
    + SVD只适用于没有缺失 必须是稠密矩阵
  + Funk SVD
    + 一个大的分成两个小矩阵
    + LFM原理
  + BiassSVD 矩阵分解+baseline
  + SVD++ 矩阵分解+baseline+其他影响（点击，收藏，购买） 

#### 基于内容的协同过滤推荐

+ 构建画像 给用户/物品打标签

  + 物品画像
    + 分类信息
    + 标题
    + 电影/音乐 主演、歌手
  + 用户画像（100个左右）
    + 喜好的物品类别 行为偏好
    + 基本人口学属性
    + 活跃程度
    + 风控维度

+ 物品标签来自哪里？

  + PGC 应用自己生成
  + UGC 用户自己生成

+ 基于内容推荐的算法流程

  + 用户画像/物品画像
  + 匹配用户画像 物品画像

+ 物品冷启动问题

  + 画像->词向量->物品向量->计算物品相似度
  + 从文本描述的角度找相似的物品
  + 当用户再浏览A的时候 通过上述套路找到跟物品A相似的一系列物品

+ 流程：

  + 1. 建立物品画像

    + 用户打tag 、电影的分类值
    + 根据电影的id 把tag和分类值合并起来 求tf-idf
    + 根据tf-idf的结果 为每一步电影筛选出top-n个关键词
    + 电影id-关键词-关键词权重

  + 2. 建立倒排索引

    + 通过关键词找到电影
    + 遍历 电影id-关键词-关键词权重，读取每一个关键词，用关键词作为key【（关键词对应的电影id，tfidf）】作为value保存到dict当中 

  + 3. 用户画像

    + 看用户看过那些电影，到电影的 电影id-关键词-关键词权重 数据中 找到电影所对应的关键词
    + 看用户看过的所有的关键词放到一起 统计词频 每个词出现了几次
    + 出现次数多的关键词 作为用户的兴趣词，这个兴趣词实际上就是用户画像的关键词

  + 4. 根据用户的兴趣词 找到兴趣词对应的电影 多个兴趣词可能对应一个电影{电影id：[关键词1权重，关键词2权重]}

    + 把每一部电影对应的关键词权重求和之后 排序 权重比较高的排在前面 推荐给用户


<span id="2"> </span>
# 大数据框架

### Hadoop概念

+ 分布式的计算框架 **可靠 可扩展性**
+ 集群可以上万台，分布式计算 分布式存储
+ 可靠high availability（HA）
+ Hadoop可以做啥：
  + 数据仓库
    + 数据库一般只保存数据的最新状态，极个别重要的值会保存历史版本
    + 数据仓库 会保存所有的历史版本 只记录 很少更新删除
  + PB级数据的存储 处理 分析 统计
    + 日志分析
    + 数据挖掘
    + BI

### Hadoop组件

+ Hadoop Common：
  + hadpoop的公共组件 去支持其他hadoop模块
+ HDFS
  + 分布式文件系统
  + 扩展性&容错性&海量数据存储
  + 数据切成指定大小的数据块进行存储
  + 数据会有冗余（多副本）保存 通过冗余实现容错
+ MapReduce 离线计算
  + 分布式计算框架
  + 数据分布式存储的，需要对分布式存储的数据做分析计算 是移动数据到一个节点 还是 移动计算到每个节点 还是 移动计算到每个节点Map Reduce移动计算
  + 扩展性&容错性&海量数据离线处理
  + 计算分成两个阶段
    + Map 分
    + Reduce 合
+ YARN
  + 资源管理 作业调度
  + 多个框架会用到HDFS上的数据，先后问题，需要有框架来协调 YARN起到了协调作用
  + 另一种资源协调者Mesos
  + Yarn hadoop2.0版本才加进来的，hadoop1.x是没有的

#### HDFS

+ 数据冗余 高可用
+ 数据拆分 配置文件中可以指定文件块大小 Block size 128MB
+ 架构
  + NameNode
    + 响应客户端请求
    + 元数据存储
    + DataNode管理
  + DataNode
    + 数据的存储
    + 和客户端之间io操作
    + 定期向NameNode汇报自身情况
+ NameNode高可用
  + 3台 1台过着 另外两个备份 zookeeper管理

#### yarn架构

+ 作用：协调多个框架共同访问hdfs集群资源
+ 架构：
  + ResourceManager：RM 资源管理器
    + 响应客户端请求
    + 管理NodeManager状态
    + 响应ApplicationMaster的请求
  + NodeManager：NM 节点管理器
    + 管理自身资源
    + 启动container 运行task
    + 响应ApplicationMaster的请求
  + ApplicationMaster：AM
    + 作业解析
    + 向ResourceManager请求资源
    + 向NodeManager分发task
  + Container 容器：封装了CPU、Memory等资源的一个容器
  + Clinent： 客户端提交作业

#### MapReduce

+ mapreduce即是分布式计算框架，也是一个编程模型
+ 解决是数据分布式存储带来的分布式计算问题
+ 把作业通过map阶段下发到每一个数据所在的节点
+ 再reduce阶段汇总map阶段的结果
+ 编程时候 要实现map接口和reduce接口
+ python脚本来写map reduce代码，是通过hadoop-streaming去实现的 最终还会编译成.jar文件执行
+ hadoop streaming 执行命令

#### MrJob

+ 写一个类继承MrJob
+ 重写mapper和reducer方法
+ 再main方法中调用MrJob.run()

#### MRJob提交作业的方式

+ 本地测试

  ```
  python mrjob代码.py 要处理的数据所在的位置
  ```

+ 提交到Hadoop集群处理

  ```
  python word_count.py -r hdfs:///test.txt -0 hdfs:///output 
  ```


+ 如果MapReduce有多个步骤可以通过steps方法指定
+ Mapreduce 慢的原因
  + 数据处理的时候 频繁再磁盘和内存上进行数据IO二不是始终再内存总处理 这些I/O操作导致了素的比较慢
  + buffer inmemory，达到80%数据时，将数据所在内存上，将这部分输出到磁盘上
+ MapReduce的架构演变
  + 1.x的时候
    + JobTracker master 计算集群管理
    + TaskTracker slave 负责具体任务执行的
    + Task Scheduler作业调度
  + 2.x Yarn出现 作业的调度都交给Yarn处理
    + MapReduce只是进行具体任务执行

#### 什么是Hive

+ 基于Hadoop数据保存到HDFS
+ 数据仓库工具
+ 结构化的数据 映射为一张数据库表
+ HQL查询功能
+ 本质 吧HQL翻译成MapReduce 降低使用hadoop计算的门槛
+ 离线的数据分析开发效率比直接用Map Reduce高

#### Hive架构

+ 用户接口
+ 元数据存储
  + 数据库 表 都保存到哪些位置上
  + 表中的字段名字 类型
  + mysql derby（自带）
+ Drive
  + 负责把HQL翻译成mapreduce
  + 或者翻译成shell命令

#### Hive和Hadoop关系

+ 利用hdfs存数据 利用mr算
+ Hive只需要跟Master节点打交道 不需要集群

#### Hive和关系型数据库区别

+ hive离线计算 海量查询
+ hive最主要做擦汗寻 不涉及删除修改 默认不支持删除修改， 默认不支持事务，并不完全支持标准sql
+ sql CRUD全部支持，支撑在线业务，索引完整 支持事务 

#### sqoop介绍

+ 作用 数据交换工具 可以实现 数据在mysql oracle<==>hdfs之间相互传递

+ 原理 通过写sqoop命令 把sqoop命令翻译成mapreduce通过mapreduce链接各种数据源 实现数据的传递

+ 通过sqoop 把mysql导入到hdfs

  + ```sql
    sqoop import --connerct jdbc:mysql//mysql数据库地址:3306 --username root --passward password --table 要到处数据的表名 -m mrjob的数量
    ```

  + 默认会把文件导入到hdfs上/usr/linux用户名 文件夹下

#### HBase介绍

+ 分布式开源数据库
+ 面向列
+ Big Table开源实现
+ 适合非结构化数据的存储
+ PB级别数据
+ 可以支撑在线业务
+ 分布式系统特点：易于扩展，支持动态伸展，并发数据处理

#### 面向列数据库

+ 关系型数据库：行式存储 每一行数据都是连续的 所有的记录都放到一个连续的存储空间中
+ 列数据库：列式存储 每一列对应一个文件 不同列并不对应连续的存储空间
+ 结构化数据 V.S. 非结构化数据
  + 结构化数据
    + 预定义的数据模型 模型一旦确定不会经常变化（表结构不会频繁调整）
  + 非结构化数据
    + 没有预定义数据模型
    + 模型不规则
    + 文本 图片 视频 音频
+ Hive 和 Hbase区别
  + hive hbase共同点
    + 都可以处理海量数据
    + 文件都是保存到hdfs上
  + hive和hbase不同
    + 计算不是同ing过mapreduce实现的 自己实现的CRUD功能
    + hive通过mapreduce实现 数据查询的
    + hbase可以有集群 集群的管理式通过zookeeper实现
    + hive只能做离线计算
    + hbase提供对数据的随机实时读、写访问功能
+ HBase对事务的支持 只支持行级别的事务
+ CAP定理
  + 分区容错性，分布式系统都要有的特性， 任何适合都要能提供服务 P保证
  + HBase CP系统 强一致性

#### Hbase 数据模型

+ NameSpace 对应 关系型数据库 database
+ 表(table): 用于存储管理数据，具有稀疏性的、面向列的特点
+ 行(row): 每一行都对应一个row key 行键Hbase 有缩影但是只是再行键 rowkey有索引
+ 列(Column): HBase的列由 Column family 和 Column qualifier 组成, 由冒号: 进行行间隔, 如 family: qualifier
+ 列族(ColumnFamily): 键值对集合 key: value

#### Hbase 和 传统关系型数据库区别

+ 创建HBase表的适合只需要指定表名 和 列族
+ 每一行当中 只需要列族相同就可以了，至于每个列族中的key: value对 key可以完全不同

### HBase基础架构

+ **Client**
  - ①与zookeeper通信, 找到数据入口地址
  - ②使用HBase RPC机制与HMaster和HRegionServer进行通信；
  - ③Client与HMaster进行通信进行管理类操作；
  - ④Client与HRegionServer进行数据读写类操作。

+ **Zookeeper**
  - ①保证任何时候，**集群中只有一个running master**，避免单点问题；
  - ②**存贮**所有Region的寻址入口，包括-ROOT-表地址、**HMaster地址**；
  - ③实时**监控Region Server的状态**，将Region server的上线和下线信息，实时通知给Master；
  - ④存储Hbase的schema，包括有哪些table，每个table有哪些column family。

+ **HMaster**

  - ①为Region server分配region；

  - ②负责region server的负载均衡；

  - ③发现失效的region serve并重新分配其上的region；

  - ④HDFS上的垃圾文件回收；

  - ⑤处理用户对表的增删改查操作。


+ **HRegionServer**

  - ①维护Master分配给它的region，处理对这些region的IO请求；

  - ②负责切分在运行过程中变得过大的region。

  - 此外，HRegionServer管理一系列HRegion对象，每个HRegion对应Table中一个Region，HRegion由多个HStore组成，每个HStore对应Table中一个Column Family的存储，Column Family就是一个集中的存储单元，故将具有相同IO特性的Column放在一个Column Family会更高效。


+ **HStore**
  - 用户写入数据的流程为：client访问ZK, ZK返回RegionServer地址-> client访问RegionServer写入数据 -> 数据存入MemStore，一直到MemStore满 -> Flush成StoreFile
  - 每一个column family对应一个Hstore


+ **HRegion**

  - 一个表最开始存储的时候，是一个region。

  - 一个Region中会有个多个store，每个store用来存储一个列簇。如果只有一个column family，就只有一个store。

  - region会随着插入的数据越来越多，会进行拆分。默认大小是10G一个。


+ **HLog**
  - 在分布式系统环境中，无法避免系统出错或者宕机，一旦HRegionServer意外退出，MemStore中的内存数据就会丢失，引入HLog就是防止这种情况。





## Spark概念

+ 基于内存的分布式计算框架
+ 只负责算 不负责存
+ spark再离线计算 功能上类似于mapreduce的作用
+ 优势：
  + 运行速度快
  + 自身生态比较完整
    + spark sql
    + spark streaming
    + spark mlib spark ml
  + api比较丰富
  + 可以使用各种语言进行操作

#### RDD的概念

+ 弹性分布数据集
+ spark当中对数据的抽象
+ 所有spark中对数据的操作最终都会转换成RDD的操作
  + spark sql
  + spark streaming
  + spark ml、mllib
+ RDD 分布式的 可容错 可以进行并行计算
+ rdd的存储可以对比hdfs
  + hdfs数据拆分成多个block rdd拆分成多个partition
  + 读取的适合 spark 加载hdfs数据 1个block对应spark rdd的一个partition
  + 写数据的适合 spark 1个partition 可能对应多个block
+ rdd式不可变的
  + 父rdd 生成一个子rdd 父rdd的状态不会变化
  + 从容错的角度去做这样的设计



#### RDD的创建

+ 创建RDD之前先要有spark context
+ 通过内存中的数据创建RDD
+ 创建RDD式可以指定partition的数量（RDD会分成几份）一个partition会对应一个task，根据cpu的内核树来指定partition（1核对应2~4个partition）

#### RDD 三类算子

- transformation
  - 所有的transformation嗾使延迟执行的，只要不调用action不会执行，只是记录过程
  - transformation这一类算子返回值还是rdd
  - rdd.transformation 还会得到新的rdd
- action
  - 会触发之前的rdd多有的transformation
  - 比如， reduce 就是一个action操作，使用某个函数聚合RDD所有元素的操作，并**返回最终计算结果**
- *persist* 操作
  - *persist*操作用于将数据缓存 可以缓存在内存中 也可以缓存到磁盘上， 也可以复制到磁盘的其它节点上

#### IP地址统计案例

+ 广播变量

  + 如果多个task要用到同一份数据，默认每个task都会复制一份
  + 用到的数据如果只是查询可以通过广播变量保存，避免数据的反复复制
  + SparkContext可以创建广播变量

  ```python
  广播变量 = sc.broadcast(值)
  广播变量.value
  ```

+ mapPartitions

  + transfomation操作
  + 类似map 但是map是一条一条传给里面的函数的，mapPartitions数据是一部分一部分传给函数的
  + 应用场景 数据处理的适合 需要来连接其他资源 如果一条一条处理 会处理一条来凝结一次，一份一份处理可以很多条数据连接一次其他资源，可以提高效率

+ 二分查找

+ ip转换

### spark standalone模式

+ Master
  + 主节点
  + 负责worker状态管理
  + 响应clentt提交的Application
+ worker
  + 管理自身资源
  + 运行Application对应的task
  + 启动图driver执行application
+ Executor
  + task最终执行application
+ Application
  + spark作业
+ Drive
  + 作业提交给spark的适合 先由一个worker启动一个Driver来分析Application
  + DAGScheduler
    + task划分 交给taskScheduler
    + 作业可以划分为多个stage
    + 每一个stage根据partition的数量决定由多少个task
  + TaskScheduler
    + 将task调度给executor执行
+ client

### spark core总结

+ spark core是spark生态最核心的部分
+ spark生态
  + spark core mapreduce
  + spark sql 类似于hive
  + spark streaming storm、flink
  + spark ML 基于DataFrame sparkmllib rdd
+ spark
  + 基于内存的 分布式计算框架
+ mapreduce和spark 优势
  + spark基于内存 速度快
  + spark api 更丰富 比mapreduce代码少
  + spark 生态完整 
    + 离线计算  spark core spark sql
    + 实时计算、流式计算 spark streaming 准实时
    + 交互式计算 spark sql dataframe
    + 机器学习
+ RDD
  + 弹性分布式数据集
  + 不可变
    + rdd->new rdd rdd和新的rdd的状态会分别保存
  + 弹性
    + 容错 可以分多个partition存 每个partition有多个副本
  + 分布式
  + 并行计算
+ RDD创建
  + spark context
    + 在内存中 list iterable
    + 从文件中加载
    + 在创建rdd的适合可以指定partitions的数量 一个partition对应一个task
+ 三类算子
  + tansformation
    + 返回rdd
    + 延迟执行 只要调用action类算子 就不会执行 只是记下了执行计算
  + action
    + 获取结果
+ spark local模式 、standalone模型
+ 广播变量

### Hbase回顾

+ 面向列 列式存储
  + 每一列数据是放在同一个文件中的，列与列之间的位置并不连续
  + 数据是一行一行存的， 每一行都是连续的
+ 非关系型数据 NoSql
  + 关系型数据 非关系型数据
+ 事务
  + 行级别事务 不是事务型数据库
+ CAP定理 CP系统

<span id="3"> </span>
# SQL学习笔记

### SQL专用名词

课程表 `courses`

```sql
+----+-------------------------+---------------+------------+------------+
| id | name                    | student_count | created_at | teacher_id |
+----+-------------------------+---------------+------------+------------+
|  1 | Advanced Algorithms     |           880 | 2020-06-01 |          4 |
|  2 | System Design           |          1350 | 2020-07-18 |          3 |
|  3 | Django                  |           780 | 2020-02-29 |          3 |
|  4 | Web                     |           340 | 2020-04-22 |          4 |
|  5 | Big Data                |           700 | 2020-09-11 |          1 |
|  6 | Artificial Intelligence |          1660 | 2018-05-13 |          3 |
|  7 | Java P6+                |           780 | 2019-01-19 |          3 |
|  8 | Data Analysis           |           500 | 2019-07-12 |          1 |
| 10 | Object Oriented Design  |           300 | 2020-08-08 |          4 |
| 12 | Dynamic Programming     |          2000 | 2018-08-18 |          1 |
+----+-------------------------+---------------+------------+------------+
```

教师表 `teachers`

```sql
+----+------------------+---------------------------+-----+---------+
| id | name             | email                     | age | country |
+----+------------------+---------------------------+-----+---------+
|  1 | Eastern Heretic  | eastern.heretic@gmail.com |  20 | UK      |
|  2 | Northern Beggar  | northern.beggar@qq.com    |  21 | CN      |
|  3 | Western Venom    | western.venom@163.com     |  28 | USA     |
|  4 | Southern Emperor | southern.emperor@qq.com   |  21 | JP      |
|  5 | Linghu Chong     | NULL                      |  18 | CN      |
+----+------------------+---------------------------+-----+---------+
```

- **数据库**：数据库是一些关联表的集合。LintCode 就是我们用来存放课程表 `courses` 和教师表 `teachers` 的数据库。
- **数据表**：数据表是数据的矩阵。课程表 `courses` 和 教师表 `teachers` 就是数据表，看起来像一个简单的 Excel 表格。
- **列：** 一列（数据元素）包含了相同类型的数据。例如教师表 `teachers` 中的姓名 `name` 就是一个列，这一列中的教师姓名都是用字符方式存储。
- **行：** 一行数据（可以称为元组，或记录）是一组相关的数据。例如教师表 `teachers` 每一行都对应一位教师信息

这个名为 `id` 的列，是我们用来唯一标识每行数据的，称为**主键**。实际上，教师的姓名是不能唯一标识一条记录的（因为教师姓名可能会有相同），所以我们使用额外的一列来唯一标识每行。这个列我们一般取名为 `id`，即 identification 的缩写，当然，我们也可以取别的名称。但需要注意的是，**一个数据表只能有一个主键**。

此外，在教师表 `teachers` 中，我们发现有一列数据 `teacher_id` 和教师表 `teachers` 中的 `id` 相关联，即每个 `teacher_id` 都指向教师表中的某一个教师记录，这种用于关联其它表某一列的列，我们称为**外键**（foreign key）。

### 什么是RDBMS？

+ RDBMS 指的是关系型数据库管理系统，全程为Relational Database Management System。
+ RDBMS 是 SQL 的基础，同样也是所有现代数据库系统的基础，比如 MS SQL Server、IBM DB2、Oracle、MySQL 以及 Microsoft Access。
+ RDBMS 中的数据存储再被称为表的数据库对象中。
+ 表是相关的数据项的集合，它由列和行组成。

### SQL语法

+ ##### 使用 SELECT COLUMN 查询单个列

  + SELECT 语句用于从数据库中选取数据，并将结果存储在一个临时结果表中，这个表称为结果集。结果集实际上也是一种包含行与列的表，只不过是未持久化的，即临时表。
  + 但其实我们在命名字段的时候，字段名可能会与 **SQL 关键字冲突**，这时候要用**反引号**将列名和表名包含一下，避免关键字冲突。因此，在本课程所有小节的学习中，都会更加严谨地加上反引号。

+ ##### 使用 SELECT COLUMN, COLUMN 查询多个列

```sql
SELECT `name`, `student_count` FROM `courses`;
```

+ ##### 使用 SELECT * 查询所有列

```sql
SELECT * FROM `courses`;
```

+ ##### 使用 SELECT DISTINCT 查询不同行

  查询的值都是唯一不重复

```sql
SELECT DISTINCT `column_name` FROM `table_name`;
```

+ ##### 使用 SELECT WHERE 对行进行筛选过滤

```sql
SELECT `column_name1`,`column_name2`… FROM `table_name` WHERE `column_name` operator `value`;
```

其中：

- column_name 对应指定列的名称，或者是多列，用逗号（ `,` ）分隔开

- table_name 对应查询表的名称

- operator 为操作符，常用的有等于 `=` 、小于 `<` 、大于 `>` 、不等于`<>` 或 `!=`。

- ##### 使用 INSERT INTO 在不指定列的情况下插入数据

```sql
INSERT INTO `table_name` VALUES (value1, value2, value3,...);
```

+ ##### 使用 INSERT INTO 在指定的列中插入数据

```sql
INSERT INTO `table_name` (`column1`, `column2`, `column3`,...) VALUES (value1, value2, value3,...);
```

+ ##### 使用 UPDATE 更新数据

```sql
UPDATE `table_name` SET `column1`=value1,`column2`=value2,... WHERE `some_column`=some_value;
```

+ **使用 DELETE 删除数据**

```sql
DELETE FROM `table_name` WHERE `some_column` = some_value;
```

+ ### 比较运算符

```sql
SELECT * 
FROM `courses`
WHERE `student_count` > 800;
```

+ ##### 使用 AND 连接多条件

```sql
SELECT `column_name` 
FROM `table_name` 
WHERE condition1 AND condition2;
```

+ ##### 使用 OR 连接多个条件

```sql
SELECT `column_name` 
FROM `table_name` 
WHERE condition1 or condition2;
```

+ ##### 使用 NOT 过滤不满足条件的数据

```sql
SELECT `column_name` 
FROM `table_name` 
WHERE NOT `condition`;
```

+ **使用 IN 查询多条件**

```sql
SELECT *
FROM `table_name`
WHERE `column_name` IN `value`;
```

+ **使用 NOT IN 排除**

```sql
SELECT *
FROM `table_name`
WHERE `column_name` NOT IN value;
```

+ **使用 BETWEEN AND 查询两值间的数据范围**

```sql
SELECT *
FROM `table_name`
WHERE `column_name` BETWEEN `value` AND `value`;
```

+ ##### 使用 IS NULL 查询空数据

```sql
SELECT *
FROM `table_name`
WHERE `column_name` IS NULL;
```

+ **使用 LIKE 模糊查询**

```sql
SELECT *
FROM `courses`
WHERE `name` LIKE 'D%';
```

| 通配符         | 描述                       |
| :------------- | :------------------------- |
| %              | 替代 0 个或多个字符        |
| _              | 替代一个字符               |
| [charlist]     | 字符列中的任何单一字符     |
| 或 [!charlist] | 不在字符列中的任何单一字符 |

**其中 'D%' 表示以 D 开头的所有单词，% 表示为通配符，可以替代 0 个或多个字符**

+ **使用 ORDER BY 对数据进行排序**

```sql
SELECT `column_name`, `column_name`
FROM `table_name`
ORDER BY `column_name`, `column_name` ASC|DESC;

SELECT `name`,`teacher_id`,`created_at`
FROM `courses`
WHERE `teacher_id` in (1,2,3)
ORDER BY `teacher_id`,`created_at`;
```

+ **使用 LIMIT 限制输出行数**

```sql
SELECT `column_name`, `column_name`
FROM `table_name`
LIMIT `offset` , `count`;

SELECT *
FROM `courses`
ORDER BY `student_count`
LIMIT 3;
```

+ ##### 使用 AVG() 函数求数值列的平均值

```sql
SELECT AVG(`column_name`) 
FROM `table_name`;

SELECT AVG(`student_count`) AS `average_student_count`
FROM `courses`;
```

+ **使用 MAX() 函数返回指定列中的最大值**

```sql
SELECT MAX(`column_name`) 
FROM `table_name`;

SELECT MAX(`student_count`) AS max_student_count
FROM `courses`;
```

+ **使用 MIN() 函数返回指定列中的最小值**

```sql
SELECT MIN(`column_name`) 
FROM `table_name`;

SELECT MIN(`student_count`) AS min_student_count
FROM `courses`;
```

+ **使用 SUM() 函数统计数值列的总数**

```sql
SELECT SUM(`column_name`) 
FROM `table_name`;

SELECT SUM(`student_count`) AS `all_student_count`
FROM `courses`;
```

+ **使用 ROUND() 函数将数值四舍五入**

```sql
SELECT ROUND(`column_name`, `decimals`) 
FROM `table_name`;

mysql> SELECT ROUND(1.388, 1);
+-----------------+
| ROUND(1.388, 1) |
+-----------------+
|             1.4 |
+-----------------+
1 row in set (0.00 sec)
```

+ **使用 NULL() 函数判断空值**

  + ISNULL()

  ```sql
  SELECT ISNULL(`column_name`)
  FROM `table_name`;
  ```

  + IFNULL()

  ```sql
  SELECT IFNULL(`column_name`, `value`)
  FROM `table_name`;
  ```

  ```sql
  SELECT `name`, `email`, ISNULL(`email`), IFNULL(`email`, 0), COALESCE(`email`, 0) 
  FROM `teachers`;
  
  mysql> SELECT `name`, `email`, ISNULL(`email`), IFNULL(`email`, 0), COALESCE(`email`, 0) FROM `teachers`;
  +------------------+---------------------------+---------------+---------------------------+---------------------------+
  | name             | email                     | ISNULL(email) | IFNULL(email,0)           | COALESCE(email,0)         |
  +------------------+---------------------------+---------------+---------------------------+---------------------------+
  | Eastern Heretic  | eastern.heretic@gmail.com |             0 | eastern.heretic@gmail.com | eastern.heretic@gmail.com |
  | Northern Beggar  | northern.beggar@qq.com    |             0 | northern.beggar@qq.com    | northern.beggar@qq.com    |
  | Western Venom    | western.venom@163.com     |             0 | western.venom@163.com     | western.venom@163.com     |
  | Southern Emperor | southern.emperor@qq.com   |             0 | southern.emperor@qq.com   | southern.emperor@qq.com   |
  | Linghu Chong     | NULL                      |             1 | 0                         | 0                         |
  +------------------+---------------------------+---------------+---------------------------+---------------------------+
  5 rows in set (0.00 sec)
  ```

+ **使用 COUNT() 函数计数**

```sql
SELECT COUNT(`column_name`) 
FROM `table_name`;

SELECT COUNT(*) 
FROM `table_name`;

SELECT COUNT(DISTINCT `column_name`) 
FROM `table_name`;
```

❗ 注意： COUNT(column_name) 与 COUNT(*) 的区别

- COUNT(column_name) 中，如果 `column_name` 字段中的值为 NULL，则计数不会增加，而如果字段值为空字符串`""`，则字段值会加 1
- COUNT(\*) 中，除非整个记录全为 NULL，则计数不会增加，如果存在某一个记录不为 NULL，或者为空字符串`""`，计数值都会加 1。正常来说，表都会有主键，而主键不为空，所以 COUNT(*) 在有主键的表中等同于 COUNT(PRIMARY_KEY)，即查询有多少条记录。

+ **使用 NOW() 、 CURDATE()、CURTIME() 获取当前时间**

  + 在 SQL 中，我们可以通过使用 **NOW()**、**CURDATE()**、**CURTIME()** 来获取当前的时间

    - `NOW()` 可以用来返回当前日期和时间 格式：YYYY-MM-DD hh:mm:ss

    - `CURDATE()` 可以用来返回当前日期 格式：YYYY-MM-DD

    - `CURTIME()` 可以用来返回当前时间 格式：hh:mm:ss
      - 在使用 `NOW()` 和 `CURTIME()` 时，如果要精确的秒以后的时间的话，可以在（）中加数字，加多少，就表示精确到秒后多少位，比如 `NOW(3)` 就是精确到毫秒，表示为： `2021-03-31 15:27:20.645`

```sql
mysql> INSERT INTO `records` VALUES (NOW(3));
Query OK, 1 row affected

mysql> SELECT `now_time` FROM `records`;
+-------------------------+
| now_time                |
+-------------------------+
| 2021-03-31 15:31:49.091 |
+-------------------------+
2 rows in set
```

+ **使用 DATE()、TIME() 函数提取日期和时间**

```sql
SELECT DATE('2021-03-25 16:16:30') AS `date`,TIME('2021-03-25 16:16:30')  AS `time`;
+------------+----------+
| date       | time     |
+------------+----------+
| 2021-03-25 | 16:16:30 |
+------------+----------+
1 row in set

mysql> SELECT DATE(NOW()),CURDATE();
+-------------+------------+
| DATE(NOW()) | CURDATE()  |
+-------------+------------+
| 2021-03-25  | 2021-03-25 |
+-------------+------------+
1 row in set

mysql> SELECT `name`, DATE(`created_at`) AS `created_date`
    -> FROM `courses`;
+-------------------------+--------------+
| name                    | created_date |
+-------------------------+--------------+
| Advanced Algorithms     | 2020-06-01   |
| System Design           | 2020-07-18   |
| Django                  | 2020-02-29   |
| Web                     | 2020-04-22   |
| Big Data                | 2020-09-11   |
| Artificial Intelligence | 2018-05-13   |
| Java P6+                | 2019-01-19   |
| Data Analysis           | 2019-07-12   |
| Object Oriented Design  | 2020-08-08   |
| Dynamic Programming     | 2018-08-18   |
+-------------------------+--------------+
10 rows in set

mysql> SELECT `name`, `created_at`, 
    -> DATE_FORMAT(DATE(`created_at`),"%Y-%m-%d") AS `created_date`,
    -> DATE_FORMAT(TIME(`created_at`),"%H:%i:%s") AS `created_time`
    -> FROM `courses`;
+-------------------------+---------------------+--------------+--------------+
| name                    | created_at          | created_date | created_time |
+-------------------------+---------------------+--------------+--------------+
| Advanced Algorithms     | 2020-06-01 09:10:12 | 2020-06-01   | 09:10:12     |
| System Design           | 2020-07-18 10:11:12 | 2020-07-18   | 10:11:12     |
| Django                  | 2020-02-29 12:10:12 | 2020-02-29   | 12:10:12     |
| Web                     | 2020-04-22 13:01:12 | 2020-04-22   | 13:01:12     |
| Big Data                | 2020-09-11 16:01:12 | 2020-09-11   | 16:01:12     |
| Artificial Intelligence | 2018-05-13 18:12:30 | 2018-05-13   | 18:12:30     |
| Java P6+                | 2019-01-19 13:31:12 | 2019-01-19   | 13:31:12     |
| Data Analysis           | 2019-07-12 13:01:12 | 2019-07-12   | 13:01:12     |
| Object Oriented Design  | 2020-08-08 13:01:12 | 2020-08-08   | 13:01:12     |
| Dynamic Programming     | 2018-08-18 20:01:12 | 2018-08-18   | 20:01:12     |
+-------------------------+---------------------+--------------+--------------+
10 rows in set
```

+ **使用 EXTRACT() 函数提取指定的时间信息**

```sql
SELECT EXTRACT(unit FROM date)
FROM `table`

mysql> SELECT `name`, EXTRACT(HOUR FROM `created_at`) AS `created_hour`
FROM `courses`;
+-------------------------+--------------+
| name                    | created_hour |
+-------------------------+--------------+
| Advanced Algorithms     | 9            |
| System Design           | 10           |
| Django                  | 12           |
| Web                     | 13           |
| Big Data                | 16           |
| Artificial Intelligence | 18           |
| Java P6+                | 13           |
| Data Analysis           | 13           |
| Object Oriented Design  | 13           |
| Dynamic Programming     | 20           |
+-------------------------+--------------+
10 row in set
```

+ **使用 DATE_FORMAT() 格式化输出日期**

```sql
SELECT DATE_FORMAT(date,format);

mysql> SELECT DATE_FORMAT(`created_at`, '%Y %m') AS `DATE_FORMAT`
    -> FROM `courses`;
+-------------+
| DATE_FORMAT |
+-------------+
| 2020 06     |
| 2020 07     |
| 2020 02     |
| 2020 04     |
| 2020 09     |
| 2018 05     |
| 2019 01     |
| 2019 07     |
| 2020 08     |
| 2018 08     |
+-------------+
10 rows in set (0.01 sec)
```

其中 %m 表示月份，%d 表示日期，%Y 表示年份，%w 表示星期。

+ **使用 DATE_ADD() 增加时间**

```sql
SELECT DATE_ADD(date, INTERVAL expr type)
FROM table_name

mysql> SELECT `name`, DATE_ADD(`created_at`, INTERVAL 1 YEAR) AS `new_created`
    -> FROM `courses`;
+-------------------------+-------------+
| name                    | new_created |
+-------------------------+-------------+
| Senior Algorithm        | 2020-06-01  |
| System Design           | 2020-07-18  |
| Django                  | 2020-02-29  |
| Web                     | 2020-04-22  |   
| Big Data                | 2020-09-11  |      
| Artificial Intelligence | 2018-05-13  |  
| Java P6+                | 2019-01-19  |    
| Data Analysis           | 2019-07-12  |   
| Object Oriented Design  | 2020-08-08  |   
| Dynamic Programming     | 2018-08-18  |   
+-------------------------+-------------+
10 rows in set (0.00 sec)
```

其中：`date` 指代希望被操作的有效日期，为起始日期

 `expr` 是希望添加的时间间隔的数值（expr 是一个字符串，对于负值的间隔，可以以 ”-“ 开头）

 `type` 是具体的数据类型，表示加上时间间隔的单位（可以是 MICROSECOND , SECOND , MINUTE , HOUR , DAY , WEEK , MONTH , QUARTER , YEAR 等）

+ **使用 DATE_SUB() 减少时间**

```sql
SELECT DATE_SUB(date, INTERVAL expr type)
FROM table_name

mysql> SELECT `id`,`name`, DATE_SUB(`created_at`, INTERVAL 1 MONTH) AS `new_created`
     > FROM `courses`;
+----+-------------------------+-------------+
| id | name                    | created_at  |
+----+-------------------------+-------------+
|  1 | Senior Algorithm        | 2020-06-01  |      
|  2 | System Design           | 2020-07-18  |      
|  3 | Django                  | 2020-02-29  |      
|  4 | Web                     | 2020-04-22  |     
|  5 | Big Data                | 2020-09-11  |          
|  6 | Artificial Intelligence | 2018-05-13  |         
|  7 | Java P6+                | 2019-01-19  |   
|  8 | Data Analysis           | 2019-07-12  |      
| 10 | Object Oriented Design  | 2020-08-08  |      
| 12 | Dynamic Programming     | 2018-08-18  |   
+----+-------------------------+-------------+
10 rows in set (0.00 sec)
```

其中：`date` 指代希望被操作的有效日期

 `expr` 是希望添加的时间间隔

 `type` 是具体的数据类型（可以是 MICROSECOND , SECOND , MINUTE , HOUR , DAY , WEEK , MONTH , QUARTER , YEAR 等）

+ **使用时间函数 DATEDIFF() 和 TIMESTAMPDIFF() 计算日期差**

```sql
SELECT DATEDIFF(时间1,时间2) AS date_diff FROM courses;

SELECT TIMESTAMPDIFF (类型,时间1,时间2) AS year_diff；
```

+ **非空约束 NOT NULL**

```sql
CREATE TABLE `Persons` (
    `ID` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255) NOT NULL,
    `Age` int
);
```

+ **唯一约束 UNIQUE**

**MySQL**

```sql
CREATE TABLE `Persons`
(
`P_Id` int NOT NULL,
`LastName` varchar(255) NOT NULL,
`FirstName` varchar(255),
`Address` varchar(255),
`City` varchar(255),
UNIQUE (`P_Id`)
)
```

**SQL Server / Oracle / MS Access**

```sql
CREATE TABLE `Persons`
(
`P_Id` int NOT NULL UNIQUE,
`LastName` varchar(255) NOT NULL,
`FirstName` varchar(255),
`Address` varchar(255),
`City` varchar(255)
)
```

命名 UNIQUE 约束，并定义多个列的 UNIQUE 约束:

**MySQL / SQL Server / Oracle / MS Access**

```sql
CREATE TABLE `Persons`
(
`P_Id` int NOT NULL,
`LastName` varchar(255) NOT NULL,
`FirstName` varchar(255),
`Address` varchar(255),
`City` varchar(255),
CONSTRAINT uc_PersonID UNIQUE (`P_Id`,`LastName`)
)
```

+ ##### 撤销 UNIQUE 约束

**MySQL**

```sql
ALTER TABLE `Persons`
DROP INDEX uc_PersonID
```

**SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
DROP CONSTRAINT uc_PersonID
```

+ **主键约束 PRIMARY KEY**

  + PRIMARY KEY 约束唯一标识数据库表中的每条记录 ，简单的说，**PRIMARY KEY = UNIQUE + NOT NULL** ,从技术的角度来看，PRIMARY KEY 和 UNIQUE 有很多相似之处。但还是有以下区别：

    - NOT NULL UNIQUE 可以将表的一列或多列定义为唯一性属性，而 PRIMARY KEY 设为多列时，仅能保证多列之和是唯一的，具体到某一列可能会重复。

    - PRIMARY KEY 可以与外键配合，从而形成主从表的关系，而 NOT NULL UNIQUE 则做不到这一点

**MYSQL**

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255),
    PRIMARY KEY (`P_Id`)
);
```

执行输出结果

```sql
mysql> CREATE TABLE `Persons`
    -> (
    ->     `P_Id` int NOT NULL,
    ->     `LastName` varchar(255) NOT NULL,
    ->     `FirstName` varchar(255),
    ->     `Address` varchar(255),
    ->     `City` varchar(255),
    ->     PRIMARY KEY (`P_Id`)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   | PRI | NULL    |       |
| LastName  | varchar(255) | NO   |     | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

> 我们通过 desc table_name 查看建立的数据表 `Persons` 可以发现，`P_Id` 的 Key 属性为 PRI ，即设定为 PRIMARY KEY 。

**SQL Server / Oracle / MS Access**

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL PRIMARY KEY,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255)
)
```

> 上述语句中 NOT NULL 为非空约束，即输入值必须不为空否则会报错。

**如需命名并定义多个列的 PRIMARY KEY 约束，请使用下面的 SQL 语法：**

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255),
    CONSTRAINT pk_PersonID PRIMARY KEY (`P_Id`,`LastName`)
)
```

> 注释：
> 在上面的实例中，只有一个主键 PRIMARY KEY（pk_PersonID）。然而，pk_PersonID 的值是由两个列（`P_Id` 和 `LastName`）组成的。

+ ##### ALTER TABLE 时添加主键约束

当表已被创建时，如需在 `P_Id` 列创建 PRIMARY KEY 约束：

**MySQL / SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
ADD PRIMARY KEY (`P_Id`)
```

执行输出结果

```sql
mysql> CREATE TABLE Persons
    -> (
    ->     `P_Id` int NOT NULL,
    ->     `LastName` varchar(255) NOT NULL,
    ->     `FirstName` varchar(255),
    ->     `Address` varchar(255),
    ->     `City` varchar(255)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE Persons
    -> ADD PRIMARY KEY (P_Id);
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   | PRI | NULL    |       |
| LastName  | varchar(255) | NO   |     | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.01 sec)
```

如需命名并定义多个列的 PRIMARY KEY 约束，可以使用下面的 SQL 语法：

**MySQL / SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
ADD CONSTRAINT pk_PersonID PRIMARY KEY (`P_Id`,`LastName`)
```

> 注释：
>
> 如果您使用 ALTER TABLE 语句添加主键，必须把主键列声明为不包含 NULL 值（在表首次创建时）。

执行输出结果

```sql
mysql> CREATE TABLE `Persons`
    -> (
    ->     `P_Id` int NOT NULL,
    ->     `LastName` varchar(255) NOT NULL,
    ->     `FirstName` varchar(255),
    ->     `Address` varchar(255),
    ->     `City` varchar(255)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> ALTER TABLE `Persons`
    -> ADD CONSTRAINT pk_PersonID PRIMARY KEY (`P_Id`,`LastName`);
Query OK, 0 rows affected (0.08 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   | PRI | NULL    |       |
| LastName  | varchar(255) | NO   | PRI | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.01 sec)
```

+ ##### 撤销 PRIMARY KEY

**MYSQL**

```sql
ALTER TABLE `Persons`
DROP PRIMARY KEY
```

执行输出结果

```sql
mysql> ALTER TABLE `Persons`
    -> DROP PRIMARY KEY;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   |     | NULL    |       |
| LastName  | varchar(255) | NO   |     | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

> 通过DESC table_name我们可以发现，Key 列上的约束已经为空了。

**SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
DROP CONSTRAINT pk_PersonID
```

+ **外键约束 FOREIGN KEY**

一个表中的 FOREIGN KEY 指向另一个表中的 UNIQUE KEY 。

**MySQL**

```SQL
CREATE TABLE `Orders`
(
`O_Id` int NOT NULL,
`OrderNo` int NOT NULL,
`P_Id` int,
PRIMARY KEY (O_Id),
FOREIGN KEY (P_Id) REFERENCES Persons(P_Id)
)
```

**SQL Server / Oracle / MS Access**

```SQL
CREATE TABLE `Orders`
(
`O_Id` int NOT NULL PRIMARY KEY,
`OrderNo` int NOT NULL,
P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
)
```

> 其中
>
> NOT NULL 表示该字段不为空
> REFERENCES 表示 引用一个表

如需命名 FOREIGN KEY 约束，并定义多个列的 FOREIGN KEY 约束:

**MySQL / SQL Server / Oracle / MS Access**

```SQL
CREATE TABLE `Orders`
(
`O_Id` int NOT NULL,
`OrderNo` int NOT NULL,
`P_Id` int,
PRIMARY KEY (O_Id),
CONSTRAINT fk_PerOrders FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
)
```

> 其中
>
> CONSTRAINT 表示约束，后面接约束名称，常用于创建约束和删除约束；

+ ##### ALTER TABLE 时的 SQL FOREIGN KEY 约束

当 "Orders" 表已被创建时，如需在 "P_Id" 列创建 FOREIGN KEY 约束:

**MySQL / SQL Server / Oracle / MS Access**

```SQL
ALTER TABLE `Orders`
ADD FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
```

如需命名 FOREIGN KEY 约束，并定义多个列的 FOREIGN KEY 约束:

**MySQL / SQL Server / Oracle / MS Access**

```SQL
ALTER TABLE `Orders`
ADD CONSTRAINT fk_PerOrders
FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
```

+ ##### 撤销 FOREIGN KEY 约束

**MySQL**

```SQL
ALTER TABLE `Orders`
DROP FOREIGN KEY fk_PerOrders
```

**SQL Server / Oracle / MS Access**

```SQL
ALTER TABLE `Orders`
DROP CONSTRAINT fk_PerOrders
```

+ **检查约束 CHECK**

  + `CHECK` 约束既可以用于某一列也可以用于某张表：

    - 如果对单个列定义 CHECK 约束，那么该列只允许特定的值。

    - 如果对一个表定义 CHECK 约束，那么此约束会基于行中其他列的值在特定的列中对值进行限制。

**MYSQL：**

```sql
CREATE TABLE `courses`
(
`id` int,
`name` varchar(255),
`student_count` int,
`created_at` date,
`teacher_id` int,
CHECK (`student_count` > 0)
)
```

**SQL Server / Oracle / MS Access：**

```sql
CREATE TABLE `courses`
(
`id` int
CHECK (`student_count` > 0),
`name` varchar(255),`student_count` int,
`created_at` date,
`teacher_id` int
)
```

- 为多个列添加 CHECK 约束

**MySQL / SQL Server / Oracle / MS Access：**

```sql
CREATE TABLE `courses`
(
`id` int,
`name` varchar(255),
`student_count` int,
`created_at` date,
`teacher_id` int,
CHECK (`student_count` > 0 AND `teacher_id` > 0)
)
```

如果想为一个表中多个字段添加约束，直接在 CHECK 关键字后的括号内添加，两个约束间使用 AND 关键字连接。

- 为 CHECK 约束命名

**MySQL / SQL Server / Oracle / MS Access：**

```sql
CREATE TABLE `courses`
(
`id` int,
`name` varchar(255),
`student_count` int,
`created_at` date,
`teacher_id` int,
CONSTRAINT chk_courses CHECK (`student_count` > 0) ;
```

核心部分的基本语法为：

```sql
[CONSTRAINT <constraint name>] CHECK (<condition>)
```

> 其中：
>
> - 约束关键字 CONSTRAINT：表示其后面接的内容为约束
> - constraint name：为约束名称
> - 关键字 CHECK：表示检查约束
> - condition：被约束内容

+  **表已存在时添加 CHECK 约束**

**MySQL / SQL Server / Oracle / MS Access：**

```sql
ALTER TABLE `courses` 
ADD CHECK ( `student_count` > 0);
```

或

```sql
ALTER TABLE `courses`  
ADD CONSTRAINT chk_courses CHECK ( `student_count` > 0 AND `teacher_id` > 0);
```

> - ALTER TABLE 关键字：表示修改表的定义
> - ADD 关键字：表示增加

+ ##### 撤销 CHECK 约束

**MYSQL：**

```sql
ALTER TABLE `courses` 
DROP CHECK chk_courses
```

**SQL Server / Oracle / MS Access：**

```sql
ALTER TABLE `courses` 
DROP CONSTRAINT chk_courses
```

+ **默认约束 DEFAULT**

  + DEFAULT 约束用法

    - DEFAULT 约束用于向列中插入默认值。

    - 如果没有规定其他的值，那么会将默认值添加到所有的新记录。

    - 例如女同学较多，性别就可以默认为“女”，如果插入一条新的记录时没有为这个字段赋值，那么系统会自动为这个字段赋值为“女”。

MYSQL / SQL Server / Oracle / MS Access

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255) DEFAULT 'Sandnes'
)
```

👇通过使用类似 GETDATE() 这样的函数, DEFAULT 约束也可以用于插入系统值：

```sql
CREATE TABLE `Orders`
(
    `O_Id` int NOT NULL,
    `OrderNo` int NOT NULL,
    `P_Id` int,
    `OrderDate` date DEFAULT GETDATE()
)
```

+ ##### ALTER TABLE 时的 DEFAULT 约束

MYSQL

```sql
ALTER TABLE `Persons`
ALTER `City` SET DEFAULT 'SANDNES'
```

SQL Server / MS Access：

```sql
ALTER TABLE `Persons`
ADD CONSTRAINT ab_c DEFAULT 'SANDNES' for `City`
```

+ ##### 撤销 DEFAULT 约束

MYSQL:

```sql
ALTER TABLE `Persons`
ALTER `City` DROP DEFAULT
```

SQL Server / Oracle / MS Access：

```sql
ALTER TABLE `Persons`
ALTER COLUMN `City` DROP DEFAULT
```

+ **联结**

### 什么叫联结？

我们可以举一个生活中的例子来理解“联结”：

👇

在大学每一个新学期即将开始的时候，我们都会遇到一个大难题，那就是选课。

假如所有课程的信息和所有教师的信息都存放在同一张表中，我们会发现这显得很繁琐且不便于信息的更改。因为同一个教师开设的不同课程对应的教师信息都是相同的，当教师信息改变时，需要修改多条课程。

因此，为了同学们在选课时操作更便利，我们根据内容将所有数据分解为了两个表，即教师表和课程表。
我们可以将他们称为**关系表**。关系表就是一类数据一个表，各表通过某些关系互相关联。

🤔
那么问题就来了，数据存储在两个表中，如果我们想用一条 SELECT 语句同时查询课程信息和对应的教师信息该怎么办呢？

答案就是——使用**联结**。

简单地说，联结是一种机制，用于在一条 SELECT 语句中关联多个表，返回一组输出。

这个时候就要说一下联结中的两大主角——**主键（PRIMARY KEY）\**和\**外键（FOREIGN KEY）**。

以我们使用的教师表和课程表为例：

**表1：courses (课程表)**

|     列名      |     类型     |     注释     |
| :-----------: | :----------: | :----------: |
|      id       | int unsigned |     主键     |
|     name      |   varchar    |   课程名称   |
| student_count |     int      |   学生总数   |
|  created_at   |     date     | 创建课程时间 |
|  teacher_id   |     int      |   讲师 id    |

**表2：teachers (教师表)**

|  列名   |  类型   |   注释   |
| :-----: | :-----: | :------: |
|   id    |   int   |   主键   |
|  name   | varchar | 讲师姓名 |
|  email  | varchar | 讲师邮箱 |
|   age   |   int   | 讲师年龄 |
| country | varchar | 讲师国籍 |

教师表 `teachers` 中包含所有教师的信息，每个教师具有唯一的标识，这个标识被称为**主键（PRIMARY KEY）**，可以为 id 或其他唯一值。我们这里选择教师编号（`id`）为教师表的主键。

而在课程表 `courses` 中，除了教师编号 `teacher_id`（即教师表的主键）外不存储任何教师的信息。则教师编号为课程表的**外键（FOREIGN KEY）**。

教师表的主键又叫作课程表的外键，因此这两个表通过教师编号这一列关联了。

```sql
`table1`.`common_field` = `table2`.`common_field`
```

+ SQL JOIN 连接子句用于将数据库中两个或者两个以上表中的记录组合起来。其类型主要分为 INNER JOIN（内连接）、OUTER JOIN（外连接）、全连接（FULL JOIN）和交叉连接（CROSS JOIN），其中 OUTER JOIN 又可以细分为 LEFT JOIN（左连接）和 RIGHT JOIN（右连接）。

+ 因此，我们主要使用的 JOIN 连接类型如下：

  - **INNER JOIN**：如果表中有至少一个匹配，则返回行

  - **LEFT JOIN**：即使右表中没有匹配，也从左表返回所有的行

  - **RIGHT JOIN**：即使左表中没有匹配，也从右表返回所有的行

  - **FULL JOIN**：只要其中一个表中存在匹配，则返回行

  - **CROSS JOIN**：又称**笛卡尔积**，两个表数据一一对应，返回结果的行数等于两个表行数的乘积

+ **内连接 INNER JOIN**

### 基本语法

在对 INNER JOIN（内连接）的概念有基本的了解之后，我们再来学习一下它的基本语法。
基本语法有如下两种写法：

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`
INNER JOIN `table2`
ON `table1`.`common_field` = `table2`.`common_field`;
```

或

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`
JOIN `table2`
ON `table1`.`common_field` = `table2`.`common_field`;
```

> 注：INNER JOIN 中 INNER 可以省略不写

其中，语法的核心部分如下所示：

```sql
FROM table1
INNER JOIN table2
ON table1.common_field = table2.common_field
```



请编写 SQL 语句，将课程表 `courses` 和教师表 `teachers` 进行内连接，查询 “Eastern Heretic” 老师所教的所有课程的课程名和课程编号 , 且结果列名分别以课程编号 `id` 、课程名称 `course_name` 和教师姓名 `teacher_name` 显示。

+ **外连接 OUTER JOIN**

在MySQL中，外连接查询会返回所操作的表中至少一个表的所有数据记录。在MySQL中，数据查询通过SQL语句 “OUTER JOIN…ON” 来实现，外连接查询可以分为以下三类：

- 左外连接
- 右外连接
- 全外连接

外连接数据查询语法如下：

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        LEFT | RIGHT | FULL  (OUTER) JOIN table2
        ON CONDITION;
```

+ **左外连接 LEFT JOIN**

外连接查询中的左外连接就是指新关系中执行匹配条件时，以关键字 LEFT JOIN 左边的表为参考表。左外连接的结果包括 **LEFT OUTER 子句**中指定的**左表的所有行**，而不仅仅是连接列所匹配的行，这就意味着，左连接会**返回左表中的所有记录**，加上右表中匹配到的记录。如果左表的某行在右表中没有匹配行，那么在相关联的结果行中，右表的所有选择列表均为空值。

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        LEFT JOIN table2
        ON CONDITION ;
```

```sql
mysql-> SELECT c.name AS course_name, t.name AS teacher_name
     -> FROM teachers t
          LEFT JOIN courses c ON c.teacher_id = t.id;
+------------------------+--------------------+
|  course_name           | teacher_name       |
+------------------------+--------------------+
| Big Data	         |Eastern Heretic     |
| Data Analysis          |Eastern Heretic     |
| Dynamic Programming	 |Eastern Heretic     | 
| NULL	                 |Northern Beggar     |
| System Design          |Western Venom       |
| Django	         |Western Venom       |
| Artificial Intelligence|Western Venom       |
| Java P6+	         |Western Venom       |
| Senior Algorithm	 |Southern Emperor    |
| Web	                 |Southern Emperor    |
| Object Oriented Design |Southern Emperor    |
| NULL	                 |Linghu Chong        |
+------------------------+--------------------+
12 rows in set (0.01 sec)
```

+ ##### 右外连接 RIGHT JOIN

外连接查询中的右外连接是指新关系中执行匹配条件时，以关键字 RIGHT JOIN 右边的表为参考表，如果右表的某行在左表中没有匹配行，左表就返回空值。

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        RIGHT JOIN table2
        ON CONDITION ;
```

```sql
mysql-> SELECT c.name AS course_name, t.name AS teacher_name, t.email AS teacher_email
     -> FROM courses c
	     RIGHT JOIN teachers t ON c.teacher_id = t.id;
+------------------------+----------------------+---------------------------+
|  course_name           | teacher_name         | teacher_email             |
+------------------------+----------------------+---------------------------+
| Dynamic Programming	 | Eastern Heretic	| eastern.heretic@gmail.com |
| Data Analysis   	 | Eastern Heretic	| eastern.heretic@gmail.com |
| Big Data   	         | Eastern Heretic	| eastern.heretic@gmail.com |
| Dynamic Programming    | Northern Beggar	| northern.beggar@qq.com    |
| Java P6+	         | Western Venom	| western.venom@163.com     |
| Artificial Intelligence| Western Venom	| western.venom@163.com     |
| Django	         | Western Venom	| western.venom@163.com     |
| System Design	         | Western Venom	| western.venom@163.com     |
| Object Oriented Design | Southern Emperor	| southern.emperor@qq.com   |
| Web	                 | Southern Emperor	| southern.emperor@qq.com   |
| Advanced Algorithms	 | Southern Emperor	| southern.emperor@qq.com   |
| NULL	                 | Linghu Chong	        | NULL                      |
+------------------------+----------------------+---------------------------+
12 rows in set (0.01 sec)
```

+ ##### 全外连接 FULL (OUTER) JOIN

FULL OUTER JOIN 关键字只要左表（table1）和右表（table2）其中一个表中存在匹配，则返回行。FULL OUTER JOIN 关键字结合了 LEFT JOIN 和 RIGHT JOIN 的结果。

> 注：MySQL 数据库不支持全连接，想要实现全连接可以使用 UNION ALL 来将左连接和右连接结果组合在一起实现全连接。

**UNION** ：联合的意思，即把两次或多次查询结果合并起来

要求：两次查询的列数必须一致，同时，每条 SELECT 语句中的列的顺序必须相同

推荐：列的类型可以不一样，但推荐查询的每一列，相对于的类型应该一样

可以来自多张表的数据：多次sql语句取出的列名可以不一致，此时以第一个sql语句的列名为准，即UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。
如果不同的语句中取出的行，有完全相同(这里表示的是每个列的值都相同)，那么 UNION 会将相同的行合并，最终只保留一行。也可以这样理解，UNION 会去掉重复的行。
如果不想去掉重复的行，可以使用 UNION ALL 。
如果子句中有 order by,limit，需用括号()包起来。推荐放到所有子句之后，即对最终合并的结果来排序或筛选。

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        LEFT JOIN table2 ON CONDITION 
UNION
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        RIGHT JOIN table2 ON CONDITION ;
```

```sql
mysql-> SELECT c.name AS course_name, t.age AS teacher_age
     -> FROM courses c
	    LEFT JOIN teachers t ON c.teacher_id = t.id
     -> UNION
     -> SELECT c.name AS course_name, t.age AS teacher_age
     -> FROM courses c
	    RIGHT JOIN teachers t ON c.teacher_id = t.id;
+------------------------+---------------+
|  course_name           | teacher_age   | 
+------------------------+---------------+
| Advanced Algorithms	 | 21            | 
| System Design	         | 28            | 
| Django                 | 28            | 
| Web Southern	         | 21            |  
| Big Data	         | 20            |  
| Artificial Intelligence| 28            | 
| Java P6+	         | 28            |  
| Data Analysis Eastern	 | 20            |  
| Object Oriented Design | 21            |  
| Dynamic Programming	 | 20            |  
| Linghu Chong 	         | 18            | 
| NULL	                 | 21            |  
| NULL	                 | 18            |  
+------------------------+----------------------+---------------------------+
13 rows in set (0.01 sec)
```

请编写 SQL 语句，将课程表 `courses` 和教师表 `teachers` 进行全外连接，查询所有课程名称以及与其相互对应的教师名称和教师国籍，结果列名请分别以课程名称 `course_name` 、教师名称 `teacher_name` 、教师国籍 `teacher_country` 显示。

+ **交叉连接 CROSS JOIN**

### 什么是交叉连接

与内连接和外连接相比，交叉连接非常简单，因为它不存在 ON 子句，那怎么理解交叉连接呢？

交叉连接：返回左表中的所有行，左表中的每一行与右表中的所有行组合。即将两个表的数据一一对应，其查询结果的行数为左表中的行数乘以右表中的行数。

CROSS JOIN（交叉连接）的结果也称作笛卡尔积，我们来简单了解一下什么是笛卡尔积：

笛卡尔乘积是指在数学中，两个集合 X 和 Y 的笛卡尓积（Cartesian product），又称直积，表示为**X × Y**，第一个对象是 X 的成员而第二个对象是 Y 的所有可能有序对的其中一个成员。

### 交叉连接的两种定义方式

交叉连接有两种定义方式，分为隐式连接和显式连接。两种定义方式的查询结果是相同的。

- 隐式交叉连接：不需要使用 CROSS JOIN 关键字，只要在 SELECT 语句的 FROM 语句后将要进行交叉连接的表名列出即可，这种方式基本上可以被任意数据库系统支持。

基本语法如下：

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`,`table2`;
```

假如我们想将课程表和教师表进行隐式的交叉连接，查询该学期所有开课老师和他们开课的课程，我们可以使用下列 SQL 语句：

```sql
SELECT  `courses`.`name` AS `course_name`, `teachers`.`name` AS `teacher_name`
FROM `courses` ,`teachers`;
```

为了编写的便利和简洁，我们一般会给表取别名，如本题中给教师表 `courses` 取别名为 `c`，给教师表`teachers` 取别名为 `t`：

```sql
SELECT  `c`.`name` AS `course_name`, `t`.`name` AS `teacher_name`
FROM `courses` `c`,`teachers` `t`;
```

- 显式交叉连接：与隐式交叉连接的区别就是它使用 CROSS JOIN 关键字，用法与 INNER JOIN 相似。

基本语法如下：

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`
CROSS JOIN `table2`;
```

使用显式交叉连接来解决上文相同的问题，我们可以使用下列 SQL 语句：

```sql
SELECT  `courses`.`name` AS `course_name`, `teachers`.`name` AS `teacher_name`
FROM `courses` 
CROSS JOIN `teachers`;
```

或

```sql
SELECT  `c`.`name` AS `course_name`, `t`.`name` AS `teacher_name`
FROM `courses` `c`
CROSS JOIN `teachers` `t`;
```

+ **GROUP BY 子句**

```sql
SELECT `column_name`, aggregate_function(`column_name`)
FROM `table_name`
WHERE `column_name` operator value
GROUP BY `column_name`;
```

+ ---请编写 SQL 语句，查询教师表 `teachers` 和课程表 `courses`,统计每个老师教授课程的数量，并将结果按课程数量降序排列，课程数量相同时按教师姓名升序排列，返回老师姓名和课程数量，分别命名为 `teacher_name` 和 `course_count`。

```sql
SELECT t.name as teacher_name, count(c.name) as course_count
FROM teachers t left join courses c 
on t.id = c.teacher_id
group by t.id
order by course_count desc, teacher_name asc;
```

+ **HAVING 子句**

HAVING 子句对于 GROUP BY 子句设置条件的方式其实与 WHERE 子句与 SELECT 的方式类似，语法也相近，但 WHERE 子句搜索条件是在分组操作之前，而 HAVING 则是在之后。

```sql
SELECT   `column_name`, aggregate_function(`column_name`) 
FROM     `table_name` 
WHERE    `column_name` operator value 
GROUP BY `column_name` 
HAVING   aggregate_function(`column_name`) operator value;
```

**现需要结合教师表与课程表，统计不同教师所开课程的学生总数，对于没有任课的老师，学生总数计为 0 ，最后查询学生总数少于 3000 的教师姓名及学生总数 （别名为 `student_count` ），结果按照学生总数升序排列，如果学生总数相同，则按照教师姓名升序排列。**

使用 SQL 中 HAVING 子句查询的方式如下：

```sql
SELECT `T`.`name` AS `name`, IFNULL(SUM(`C`.`student_count`),0) AS `student_count`
FROM `courses` `C` RIGHT JOIN `teachers` `T`
ON `C`.`teacher_id` = `T`.`id`
GROUP BY `T`.`id`
HAVING `student_count` < 3000
ORDER BY `student_count`, `name`;
```

执行输出结果：

```sql
mysql> SELECT `T`.`name`, IFNULL(SUM(`C`.`student_count`),0) AS `student_count`
    -> FROM `courses` `C` 
    -> RIGHT JOIN `teachers` `T` ON `C`.`teacher_id` = `T`.`id`
    -> GROUP BY `T`.`id`
    -> HAVING `student_count` < 3000
    -> ORDER BY `student_count`, `name`;
+------------------+---------------+
| name             | student_count |
+------------------+---------------+
| Linghu Chong     |             0 |
| Northern Beggar  |             0 |
| Southern Emperor |          1520 |
+------------------+---------------+
3 rows in set (0.02 sec)
```

请编写 SQL 语句，从 `teachers` 表中，筛选出同一国家的教师平均年龄大于所有教师平均年龄的国家，并获取这些国家的所有教师信息。

+ **SELECT 语句中的子查询**

```sql
SELECT `column_name(s)`
FROM `table_name`
WHERE `column_name` OPERATOR (
    SELECT `column_name(s)`
    FROM `table_name`
);
```

请编写 SQL 语句，查询 `courses` 表和 `teachers` 表，查询 'Big Data' 课程对应的老师姓名。

+ **INSERT 语句中的子查询**

```sql
INSERT INTO `table_name`
	SELECT `colnum_name(s)`
	FROM `table_name`
	[ WHERE VALUE OPERATOR ]
```

请编写 SQL 语句，将教师表 teachers 中年龄大于 20（不包括 20） 的教师的数据复制到与它结构相同的空表 teachers_bkp 表中。

+ **UPDATE 语句中的子查询**

```sql
UPDATE `table_name` 
SET `column_name` = `new_value`
WHERE `column_name` OPERATOR 
   (SELECT `column_name`
   FROM `table_name`
   [WHERE] )
```

请编写 SQL 语句，从 teachers 表中查询教师名字为 Eastern Heretic 的信息，并根据教师 id 将教师 Eastern Heretic 创建的课程名称全部改为 PHP，并将学生总数设为 300 人。

+ **DELETE 语句中的子查询**

```sql
DELETE FROM `table_name`
WHERE `column_name` OPERATOR 
   (SELECT `column_name`
   FROM `table_name`  
   [WHERE] )
```

请编写 SQL 语句，删除教师表 `teachers` 中在 2020 年前（不包括 2020 年）创建过课程的教师。

+ **内联视图子查询**

```sql
SELECT *
FROM (
	SELECT *
	FROM `teachers`
	WHERE `country` = 'USA'
) `T`
WHERE `age` = (
	SELECT MAX(`age`)
	FROM `teachers`
);
```

请编写 SQL 语句，使用**内连接（INNER JOIN）**，连接 `teachers` 表和 `courses` 表，并将查询的课程名称（起别名为 `course_name`）、学生总数、讲师姓名（起别名为 `teacher_name`）信息作为查询表，并从该查询表中选出学生总数最多的信息。

+ **IN 操作符的多行子查询**

```sql
SELECT `column_name`
FROM `table_name`
WHERE `column_name` IN(
    SELECT `column_name`
    FROM `table_name`
    WHERE `column_name` = VALUE
);
```

请编写 SQL 语句，联合教师表和课程表，查询课程表 `courses` 中所有年龄大于 20 岁的教师所教的所有课程的课程名（name）。

+ **ANY 操作符的多行子查询**

```sql
SELECT `column_name(s)`
FROM `table_name`
WHERE `column_name` OPERATOR
   ANY(SELECT column_name
   FROM table_name)
```

请编写 SQL 语句，对于 `Southern Emperor` 教师的**所有**课程，从 `courses` 表和 `teachers` 表中查询课程创建时间晚于其中**任意一门**课程创建时间的课程名称（不包含该教师的课程）。

+ **ALL 操作符的多行子查询**

```sql
SELECT `column_name(s)`
FROM `table_name`
WHERE `column_name` OPERATOR
   ALL(SELECT column_name
   FROM table_name)
```

请编写 SQL 语句，查询教师表 `teachers` 和课程表 `courses`，查询最年长的老师所开课程的学生数，最后返回学生数均超过这些课程的课程信息。

+ **多列子查询**

```sql
SELECT `name`, `age`, `country` 
FROM `teachers` 
WHERE (`country`, `age`) IN ( 
        SELECT `country`, MAX(`age`) 
        FROM `teachers` 
        GROUP BY `country` 
);
```

请编写 SQL 语句，从课程表 `courses` 中查询每个教师授课学生人数最高的课程名称 `name` 和上课人数 `student_count`。

+ **HAVING 子句中的子查询**

```sql
SELECT `name`
FROM `teachers`
WHERE `id` IN (
	SELECT `teacher_id`
	FROM `courses`
	GROUP BY `teacher_id`
	HAVING AVG(`student_count`) > (
		SELECT AVG(`student_count`)
		FROM `courses`
	)
);
```

请编写 SQL 语句，连接 `courses` 与 `teachers` 表，统计不同国籍教师所开课程的学生总数，对于没有任课的老师，学生总人数计为 0 。最后查询教师国籍是 'U' 开头且**这个国籍的所有教师**的学生总数在 **2000** 到 **5000** 之间（包括 2000 和 5000）的教师国籍及学生总数 （别名为 `student_count` ），结果按照学生总数降序排列，如果学生总数相同，则按照教师国籍升序排列。

+ MySQL Transaction(事务)

  + 为了理解什么是 MySQL 中的事务，让我们看一下在我们的样本数据库中添加一个新的销售订单的例子。添加一个销售订单的步骤如下所述：

    - 首先，从 `orders` 表中查询最新的销售订单号，并使用下一个销售订单号作为新的销售订单号。
    - 接下来，在 `orders` 表中插入一个新的销售订单。
    - 然后，获得新插入的销售订单号
    - 之后，将新的销售订单项目与销售订单号一起插入到 `orderdetails` 表中
    - 最后，从 `orders` 和 `orderdetails` 表中选择数据，以确认这些变化。

    现在，想象一下，如果上述一个或多个步骤由于某些原因（如表锁定）而失败，销售订单数据会发生什么？例如，如果将订单的项目添加到 `orderdetails` 表中的步骤失败，你将会有一个空的销售订单。

    这就是为什么事务处理要来拯救你。MySQL 事务允许你执行一组 MySQL 操作，以确保数据库从不包含部分操作的结果。在一组操作中，如果其中一个操作失败，就会发生回滚，将数据库恢复到其原始状态。如果没有发生错误，整个语句集就会提交到数据库中。

### 事务

##### MySQL 事务语句

MySQL 事务**允许**你执行**一组 MySQL 操作**，以确保数据库从不包含部分操作的结果。在一组操作中，如果其中一个操作失败，就会发生回滚，将数据库恢复到其原始状态。如果没有发生错误，整个语句集就会提交到数据库中。

+ MySQL 为我们提供了以下重要语句来控制事务：

  - 为了启动一个事务，你使用 `START TRANSACTION` 语句。`BEGIN` 或 `BEGIN WORK` 是 `START TRANSACTION` 的别名。

  - 要提交当前事务并使其变化永久化，你要使用 `COMMIT` 语句。

  - 要回滚当前事务并取消其变化，你可以使用 `ROLLBACK` 语句。

  - 要禁用或启用当前事务的自动提交模式，你可以使用 `SET autocommit` 语句。

```sql
-- 1. start a new transaction
START TRANSACTION;

-- 2. Get the latest order number
SELECT 
    @orderNumber:=MAX(orderNUmber)+1
FROM
    orders;

-- 3. insert a new order for customer 145
INSERT INTO orders(orderNumber,
                   orderDate,
                   requiredDate,
                   shippedDate,
                   status,
                   customerNumber)
VALUES(@orderNumber,
       '2005-05-31',
       '2005-06-10',
       '2005-06-11',
       'In Process',
        145);
        
-- 4. Insert order line items
INSERT INTO orderdetails(orderNumber,
                         productCode,
                         quantityOrdered,
                         priceEach,
                         orderLineNumber)
VALUES(@orderNumber,'S18_1749', 30, '136', 1),
      (@orderNumber,'S18_2248', 50, '55.09', 2); 
      
-- 5. commit changes    
COMMIT;
```

我们要向 `teachers` 表中插入一条 `Xie Xun` 的信息，其年龄为 49 岁，国籍为 `CN`，请补充 SQL 语句，来实现插入 `Xie Xun` 的信息。

我们要向 `teachers` 表中插入一条 `Kansas` 的信息，其年龄为 41 岁，国籍为 `UK`，请补充 SQL 语句，来实现插入 `Kansas` 的信息。

## 锁

#### 锁的解释

锁：计算机协调多个进程或线程并发访问某一资源的机制。

#### 锁的重要性

在数据库中，除了传统的计算资源（CPU、RAM、I\O等）的争抢，数据也是一种供多用户共享的资源。

因此，如何保证数据并发访问的一致性，有效性，是所有数据库必须要解决的问题。
锁冲突也是影响数据库并发访问性能的一个重要因素，因此锁对数据库尤其重要。

### 锁的缺点

加锁是消耗资源的，锁的各种操作，包括：获得锁、检测锁是否已经解除、释放锁等，都会增加系统的开销。

```sql
SHOW VARIABLES LIKE '%isolation%'
```

### **InnoDB 锁的种类之自增锁**

```sql
show variables like '%innodb_autoinc_lock_mode%';
```

- **0**：`traditonal` 每次 `insert` 语句执行都会产生表锁
- **1**：`consecutive simple insert` 会获得批量的锁，保证一批插入自增序列的连续性，插入之前提前释放锁，在这个模式下你会发现当你 `insert` 多条数据回滚的时候虽然 DB 没有插入数据，但是自增 ID 已经增长了，也是数据库默认的级别
- **2**：`interleaved` 不会锁表，实时插入，并发最高，但是基于主从的复制是不安全的，感兴趣可以去查询 RBR 和 SBR 的优缺点

通过查询知道，我们的数据库是开启 `consecutive` 级别，`simple insert` 表示通过分析 `insert` 语句可以确定插入的数量；如：`insert, insert, insert … valies(), values(), values()；`相应的其他插入方式还有 `Bulk inserts,Mixed-mode inserts` 等。
官方原话如下

> An AUTO-INC lock is a special table-level lock taken by transactions inserting into tables with AUTO_INCREMENT columns. In the simplest case, if one transaction is inserting values into the table, any other transactions must wait to do their own inserts into that table, so that rows inserted by the first transaction receive consecutive primary key values.

自增锁是一种特殊的表级别锁（table-level lock），专门针对事务插入 `AUTO_INCREMENT` 类型的列。最简单的情况，如果一个事务正在往表中插入记录，所有其他事务的插入必须等待，以便第一个事务插入的行，是连续的主键值。
