hbase Java API

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
