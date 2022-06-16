# Recommendation-system-study-notes
Recommendation system study notes
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
