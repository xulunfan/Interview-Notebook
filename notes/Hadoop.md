# Hbase
HBase是建立在Hadoop文件系统之上的分布式面向列的数据库。它是一个开源项目，是横向扩展的。

HBase是一个数据模型，类似于谷歌的Bigtable设计，可以提供快速随机访问海量结构化数据。它利用了Hadoop的文件系统（HDFS）提供的容错能力。
它属于Hadoop的生态系统，提供对数据的随机实时读/写访问，是Hadoop文件系统的一部分。
人们可以直接或通过HBase的存储HDFS数据。使用HBase在HDFS读取消费/随机访问数据。HBase在Hadoop的文件系统之上，并提供了读写访问。

# HBase的存储机制

HBase是一个面向列的数据库，在表中它由行排序。表模式定义只能列族，也就是键值对。一个表有多个列族以及每一个列族可以有任意数量的列。后续列的值连续地存在磁盘上。表中的每个单元格值都具有时间戳。总之，在一个HBase中：
    表是行的集合。
    行是列族的集合。
    列族是列的集合。
    列是键值对的集合
# MapReduce on HBase
在HBase系统上运行批处理运算，最方便和实用的模型依然是MapReduce
HBase Table和Region的关系，比较类似HDFS File和Block的关系，HBase提供了配套的TableInputFormat和TableOutputFormat API，可以方便的将HBase Table作为Hadoop MapReduce的Source和Sink，对于MapReduce Job应用开发人员来说，基本不需要关注HBase系统自身的细节。
# HBase系统架构
Client
HBase Client使用HBase的RPC机制与HMaster和HRegionServer进行通信，对于管理类操作，Client与HMaster进行RPC；对于数据读写类操作，Client与HRegionServer进行RPC
# Zookeeper
Zookeeper Quorum中除了存储了-ROOT-表的地址和HMaster的地址，HRegionServer也会把自己以Ephemeral方式注册到Zookeeper中，使得HMaster可以随时感知到各个HRegionServer的健康状态。此外，Zookeeper也避免了HMaster的单点问题，见下文描述
# HMaster
HMaster没有单点问题，HBase中可以启动多个HMaster，通过Zookeeper的Master Election机制保证总有一个Master运行，HMaster在功能上主要负责Table和Region的管理工作：
1. 管理用户对Table的增、删、改、查操作
2. 管理HRegionServer的负载均衡，调整Region分布
3. 在Region Split后，负责新Region的分配
4. 在HRegionServer停机后，负责失效HRegionServer 上的Regions迁移
# HRegionServer
HRegionServer主要负责响应用户I/O请求，向HDFS文件系统中读写数据，是HBase中最核心的模块。
HRegionServer内部管理了一系列HRegion对象，每个HRegion对应了Table中的一个Region，HRegion中由多个HStore组成。每个HStore对应了Table中的一个Column Family的存储，可以看出每个Column Family其实就是一个集中的存储单元，因此最好将具备共同IO特性的column放在一个Column Family中，这样最高效。
HStore存储是HBase存储的核心了，其中由两部分组成，一部分是MemStore，一部分是StoreFiles。
MemStore是Sorted Memory Buffer，用户写入的数据首先会放入MemStore，当MemStore满了以后会Flush成一个StoreFile（底层实现是HFile），当StoreFile文件数量增长到一定阈值，会触发Compact合并操作，将多个StoreFiles合并成一个StoreFile，合并过程中会进行版本合并和数据删除，因此可以看出HBase其实只有增加数据，所有的更新和删除操作都是在后续的compact过程中进行的，这使得用户的写操作只要进入内存中就可以立即返回，保证了HBase I/O的高性能。

# Hbase Java API

一、几个主要 Hbase API 类和数据模型之间的对应关系：

1、 HBaseAdmin
关系： org.apache.hadoop.hbase.client.HBaseAdmin
作用：提供了一个接口来管理 HBase 数据库的表信息。它提供的方法包括：创建表，删除表，列出表项，使表有效或无效，以及添加或删除表列族成员等。

2、 HBaseConfiguration
关系： org.apache.hadoop.hbase.HBaseConfiguration
作用：对 HBase 进行配置

3、 HTableDescriptor
关系： org.apache.hadoop.hbase.HTableDescriptor
作用：包含了表的名字极其对应表的列族

4、 HColumnDescriptor
关系： org.apache.hadoop.hbase.HColumnDescriptor
作用：维护着关于列族的信息，例如版本号，压缩设置等。它通常在创建表或者为表添 加列族的时候使用。列族被创建后不能直接修改，只能通过删除然后重新创建的方式。
列族被删除的时候，列族里面的数据也会同时被删除。

5、 HTable
关系： org.apache.hadoop.hbase.client.HTable
作用：可以用来和 HBase 表直接通信。此方法对于更新操作来说是非线程安全的。

6、 Put
关系： org.apache.hadoop.hbase.client.Put
作用：用来对单个行执行添加操作

7、 Get
关系： org.apache.hadoop.hbase.client.Get
作用：用来获取单个行的相关信息

8、 Result
关系： org.apache.hadoop.hbase.client.Result
作用：存储 Get 或者 Scan 操作后获取表的单行值。使用此类提供的方法可以直接获取值 或者各种 Map 结构（ key-value 对） 
