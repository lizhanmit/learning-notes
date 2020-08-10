# Data Engineer Interview Preparation

[大数据面试题知识点分析](https://blog.csdn.net/qq_26803795/article/category/7412168)

## Hadoop

### 简述一下hdfs的数据压缩算法，工作中用的是那种算法，为什么？

1.在HDFS之上将数据压缩好后，再存储到HDFS

2.在HDFS内部支持数据压缩，这里又可以分为几种方法：

  2.1 压缩工作在DataNode上完成，这里又分两种方法：

    2.1.1 数据接收完后，再压缩

    这个方法对HDFS的改动最小，但效果最低，只需要在block文件close后，调用压缩工具，将block文件压缩一下，然后再打开block文件时解压一下即可，几行代码就可以搞定

    2.1.2 边接收数据边压缩，使用第三方提供的压缩库

    效率和复杂度折中方法，Hook住系统的write和read操作，在数据写入磁盘之前，先压缩一下，但write和read对外的接口行为不变，比如：原始大小为100KB的数据，压缩后大小为10KB，当写入100KB后，仍对调用者返回100KB，而不是10KB

  2.2 压缩工作交给DFSClient做，DataNode只接收和存储
  
  这个方法效果最高，压缩分散地推给了HDFS客户端，但DataNode需要知道什么时候一个block块接收完成了。

推荐最终实现采用2.2这个方法，该方法需要修改的HDFS代码量也不大，但效果最高。

---

### NameNode和DataNode的通信原理

客户端向DataNode发出RPC请求后，DataNode会向NameNode请求获取block快，NameNode根据DataNode的块报告和心跳, 会返回给DataNode指令. 通过这种方式NameNode间接地和DataNode进行通信，实际上NameNode作为Server端, 是不会主动去联系DataNode的, 只有作为客户端的DataNode才会去联系NameNode.

---

## Hive

### hive的计算是通过什么实现的？

hive是搭建在Hadoop集群上的一个SQL引擎，它将SQL语句转化成了MapReduce程序在Hadoop上运行，所以hive的计算引擎是MapReduce，而hive的底层存储采用的是HDFS

---

### hive 支持 not in 吗?

不支持，可以用left join 实现此功能。

---

### Hive 有哪些方式保存元数据，各有哪些优缺点？

- 存储于内存数据库derby，此方法只能开启一个hive客户端，**不推荐使用**。
- 存储于mysql数据库，可以多客户端连接，推荐使用。
  - 分为本地mysql数据库，远程mysql数据库，但是本地的mysql数据用的比较多，因为本地读写速度都比较快。

---

### hive 如何权限控制？

Hive的权限需要在hive-site.xml文件中设置才会起作用，配置默认的是false。需要把hive.security.authorization.enabled设置为true，并对不同的用户设置不同的权限，例如select ,drop等的操作。

---

### hive 中的压缩格式 RCFile、 TextFile、 SequenceFile 各有什么区别？

- TextFile：默认格式，数据不做压缩，磁盘开销大，数据解析开销大。
- SequenceFile：Hadoop API提供的一种二进制文件支持，使用方便，可分割，可压缩，支持三种压缩，NONE，RECORD，BLOCK。
- RCFILE：是一种行列存储相结合的方式。首先，将数据按行分块，保证同一个record在同一个块上，避免读一个记录读取多个block。其次，块数据列式存储，有利于数据压缩和快速的列存取。数据加载的时候性能消耗大，但具有较好的压缩比和查询响应。

---

## HBase

### hive VS hbase

- hbase与hive都是架构在hadoop之上的。都是用hadoop作为底层存储
- Hive是建立在Hadoop之上为了减少MapReduce jobs编写工作的批处理系统，HBase是为了支持弥补Hadoop对实时操作的缺陷的项目 。
- 想象你在操作RMDB数据库，如果是全表扫描，就用Hive+Hadoop,如果是索引访问，就用HBase+Hadoop 。
- Hive query就是MapReduce jobs可以从5分钟到数小时不止，HBase是非常高效的，肯定比Hive高效的多。
- Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑。
- hive借用hadoop的MapReduce来完成一些hive中的命令的执行
- hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作。
- hbase是列存储。
- hdfs作为底层存储，hdfs是存放文件的系统，而Hbase负责组织文件。
- hive需要用到hdfs存储文件，需要用到MapReduce计算框架。

---

### 谈谈HBASE底层的理解

(1)HBASE主要分为HMaster和HRegionServer，HMaster主要负责表和Region的管理，负责表的增删改查，管理HRagionServer的负载均衡和Region的分布，还负责HRegionServer失效后Region的转移

(2)HRegionServer主要负责存储HRegion，每一个HRegion上有多个Hstore(对应表中的列簇)，当写入数据时，Hstore中的memstore会将数据写入缓存，当缓存写满后(默认64M)，会出发flush将缓存里的数据flush到磁盘形成storefile文件，storefile文件是Hfile的轻量级包装，Hfile是附带索引格式的文件

---

### hbase 读写数据的原理

#### 客户端读取信息流程

（1）client要读取信息，先查询下client 端的cache中是否存在数据，如果存在，直接返回数据。如果不存在，则进入到zookeeper，查找到里面的相应数据存在的Root表中的地址。

（2）BlockCache;设计用于读入内存频繁访问的数据，每个列族都有。

（3）通过数据存在ROOT表中地址找到.META，最终找到HRegion。找到HRegion后，它会先访问MemStore中是否存在数据，如果存在，则直接读取。如果没有，就再到HFile中查找数据，并将数据放到MemStore。

（4）最后数据返回到客户端显示。
 
 
#### 存储数据流程

由于Hbase中默认的刷写方式是隐式刷写，所以你在put()数据时，它会自动保存到HRegion上，但当你批量处理数据时，它会将数据先保存到client端的cache中。当你关闭隐式刷写时，你put()的数据则会保存到client cache中，直到你调用刷写命令时，才会保存到HRegion中。
 
在HRegion部分的存储：要写入的数据会先写到HMemcache和Hlog中，HMemcache建立缓存，Hlog同步Hmemcache和Hstore的事务日志，发起Flush Cache时，数据持久化到Hstore中，并清空HMemecache。
 
hbase正常写入数据时，会写入两个地方：预写式日志(WAL_or_Hlog)和Memstore(内存里的写入缓冲区), 首先写入cache，并记入WAL，然后才写入MemStore，(都写入才认为动作完成)保证数据的持久化，Hbase中的数据永久写入之前都在MemStore，当MemStore填满后，其中的数据就会写入硬盘生成HFile，
 
HBase写数据，如果在写入HStore时发生系统异常，就可以从HLog中恢复数据，重新写 HStore中。
 
Hbase的删除不会立即删除内容，会先打删除标签，直到执行一次大合并(major compaction),被删除的空间才会被释放
  
代码层次分析： HTable.put(put)
获取HTable对hTable->hTable.put(put)->put的数据存LinkedList<Row>->若AutoFlush=true，立即发送请求到服务器端，更新hbase；若AutoFlush=false，当缓冲区数据大于指定的HeadSize时，发送服务器更新hbase。
 
实际底层是开启多个线程来执行更新数据的。

---

### HBase 过滤器

HBase为筛选数据提供了一组过滤器，通过这个过滤器可以在HBase中的数据的多个维度（行，列，数据版本）上进行对数据的筛选操作，也就是说过滤器最终能够筛选的数据能够细化到具体的一个存储单元格上（由行键，列名，时间戳定位）。通常来说，通过行键，值来筛选数据的应用场景较多。

---

### Hbase 过滤器实现原理

采用bloomfilter进行过滤，Bloom Filter是一种空间效率很高的随机数据结构

（1）BLOOMFILTER在HBase的作用
HBase利用 BLOOMFILTER来提供随机读（GET）的性能，对于顺序读（Scan），设置BLOOMFILTER是没有作用的。

（2）BLOOMFILTER在HBase的开销
BLOOMFILTER是一个列族级别的配置，如果你表中设置了BLOOMFILTER，那么HBase在生成StoreFile时候包含一份BLOOMFILTER的结构数据，称为MetaBlock；开启BLOOMFILTER会有一定的存储以及内存的开销。

（3）BLOOMFILTER如何提供随机读（GET）的性能
对于某个region的随机读，HBase会遍历读memstore及storefile（按照一定的顺序），将结果合并返回给客户端。如果你设置了bloomfilter，那么在遍历读storefile时，就可以利用bloomfilter，忽略某些storefile。

（4）Region的StoreFile数目越多，BLOOMFILTER效果越好。

（5）Region下的storefile数目越少，HBase读性能越好。

---

### Hbase热点写问题

1.热点写问题表现在大量的写请求集中在一个region上，造成单点压力大，降低写效率. 

2.解决方法.创建表的指定多个region，默认情况下一个表一个region，刚开始写的时候就会造成所有的写请求都写到一个region上面，创建多个region的话，写请求就会分流到多个region上面去。提高写的效率 

3.第二个方法，对rowkey进行散列，既然我们要把多个请求写分到不同的region上，我们需要对key进行md5，进行散列，这样就可以把写请求分到不同的region上面去。大幅提高效率。 

4.上面两个方法要一起使用，如果只分多个region而不进行散列的话，rowkey递增的话，还是会造成多个写集中到一个region上

Hbase默认建表时有一个region，这个region的rowkey是没有边界的，即没有startkey和endkey，在数据写入时，所有数据都会写入这个默认的region，随着数据量的不断 增加，此region已经不能承受不断增长的数据量，会进行split，分成2个region。在此过程中，会产生两个问题：1.数据往一个region上写,会有写热点问题。2.region split会消耗宝贵的集群I/O资源。基于此我们可以控制在建表的时候，创建多个空region，并确定每个region的起始和终止rowky，这样只要我们的rowkey设计能均匀的命中各个region，就不会存在写热点问题。自然split的几率也会大大降低。当然随着数据量的不断增长，该split的还是要进行split。

### 如果用HBase 直接将时间戳作为行健，在写入单个 region 时候会发生热点问题，为什么？

HBase的rowkey在底层是HFile存储数据的，以键值对存放到SortedMap中。并且region中的rowkey是有序存储，若时间比较集中。就会存储到一个region中，这样一个region的数据变多，其它的region数据比较少，加载数据就会很慢。直到region split可以解决。

---

### 如何提高 HBase 客户端的读写性能

- 开启bloomfilter过滤器，开启bloomfilter比没开启要快3、4倍。
- hbase对于内存有特别的嗜好，在硬件允许的情况下配足够多的内存给它通过修改hbase-env.sh中的：
`export HBASE_HEAPSIZE=3000 #这里默认为1000m`
- 修改Java虚拟机属性。替换掉默认的垃圾回收器，因为默认的垃圾回收器在多线程环境下会有更多的wait等待：  
`export HBASE_OPTS="-server -XX:NewSize=6m -XX:MaxNewSize=6m -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode"`
- 增大RPC数量：通过修改hbase-site.xml中的hbase.regionserver.handler.count属性，可以适当的放大。默认值为10有点小。
- 做程序开发需要注意的地方

```
//需要判断所求的数据行是否存在时，尽量不要用下面这个方法
HTable.exists(final byte [] row)
//而用带列族的方法替代，比如：
HTable.exists(final byte [] row, final byte[]column)

//判断所求的数据是否存在不要使用
HTable.get(final byte [] row, final byte []column) == null
//而应该用下面的方法替代：
HTable.exists(final byte [] row, final byte[] column)
```

---

### HBase 接收数据，如果短时间导入数量过多的话就会被锁, 该怎么办？

通过调用Htable.setAutoFlush(false)方法可以将htable写客户端的自动flush关闭，这样可以批量写入到数据到hbase。而不是有一条put 就执行一次更新，只有当put填满客户端写缓存时，才实际向Hbase 服务端发起请求。默认情况下auto flush 是开启的。

---

### hbase 宕机如何处理

HLog记录数据的所有变更，一旦region server 宕机，就可以从log中进行恢复。

---

### 怎样将mysql的数据导入到hbase中？

- 一种可以加快批量写入速度的方法是通过预先创建一些空的regions，这样当数据写入hbase时，会按照region分区情况，在集群内做数据的负载均衡。
- hbase 里面有这样一个hfileoutputformat类，他的实现可以将数据转换成hfile格式，通过new一个这个类，进行相关配置，这样会在Hdfs下面产生一个文件，这个时候利用hbase提供的jruby的loadtable.rb脚本就可以进行批量导入。

---

### hbase的快速查找建立在哪些因素的基础上？

有且仅有一个：rowkey（行键），rowkey是hbase的key-value存储中的key，通常使用用户要查询的字段作为rowkey，查询结果作为value。可以通过设计满足几种不同的查询需求。

- 数字rowkey的从大到小排序：原生hbase只支持从小到大的排序，这样就对于排行榜一类的查询需求很尴尬。那么采用rowkey = Integer.MAX_VALUE-rowkey的方式将rowkey进行转换，最大的变最小，最小的变最大。在应用层再转回来即可完成排序需求。
- rowkey的散列原则：如果rowkey是类似时间戳的方式递增的生成，建议不要使用正序直接写入rowkey，而是采用reverse的方式反转rowkey，使得rowkey大致均衡分布，这样设计有个好处是能将regionserver的负载均衡，否则容易产生所有新数据都在一个regionserver上堆积的现象，这一点还可以结合table的预切分一起设计。

---

### HBase 的瓶颈

HBase的瓶颈就是硬传输速度，Hbase 的操作，它可以往数据里面 insert，也可以update一些数据，但update 的实际上也是insert，只是插入一个新的时间戳的一行，delete数据，也是insert，只是insert一行带有delete标记的一行。hbase的所有操作都是追加插入操作。hbase是一种日志集数据库。它的存储方式，像是日志文件一样。它是批量大量的往硬盘中写，通常都是以文件形式的读写。这个读写速度，就取决于硬盘与机器之间的传输有多快。

---

## Kafka

### kafka如何保证数据不丢失

#### 生产者数据不丢失

生产者发送数据有同步方式和异步方式，先来看一下生产者一些关键配置项，

##### 同步方式下配置

```
producer.type=sync 
request.required.acks=1
```

在同步方式下发送数据，在发送的时候会产生阻塞，等待ack反馈，acks有三个参数0, 1，all，acks参数设置为0表示不等待反馈，表示不需要等待kafka完成同步确认接收消息，风险很大，数据可能丢失，设置为1表示必须kafka集群中的leader节点接收到消息并确认当前的生产者可以发送下一条消息，但是如果这个时候leader挂掉了，集群中尚未完成其他机器的同步，这时候导致数据丢失，设置为all表示等待kafka集群所有节点反馈，可以保证不丢失数据，三种参数的性能上有差异，比如一些允许丢失的消息，又想提升吞吐量，可以配置成0

##### 异步方式下配置

```
producer.type=async 
request.required.acks=1 
queue.buffering.max.ms=5000 
queue.buffering.max.messages=10000 
queue.enqueue.timeout.ms = -1 
batch.num.messages=200
```

producer.type=async表示异步方式

request.required.acks=1表示leader接收到消息并反馈 上面的这些参数其他值已经解释了

queue.buffering.max.ms=5000表示异步模式下缓冲数据的最大时间。例如设置为100则会集合100ms内的消息后发送，这样会提高吞吐量，但是会增加消息发送的延时

queue.buffering.max.messages=10000表示异步模式下缓冲的最大消息数，同上

queue.enqueue.timeout.ms=-1表示异步模式下，消息进入队列的等待时间。若是设置为0，则消息不等待，如果进入不了队列，则直接被抛弃

batch.num.message=200表示异步模式下，每次发送的消息数，当queue.buffering.max.messages或queue.buffering.max.ms满足条件之一时producer会触发发送

通过以上配置能保证同步或者异步的情况下生产者不丢失数据

#### 消费者数据不丢失

kafka里面一个主题可以有多个分区，一个分区同时只能被同一个消费者组中的一个消费者消费，消费者组可能包含多个消费者，消费者组下面会记录某个主题某个分区的offset值，来记录消费的到哪里

具体偏移量可以在zk中输入指令，后面的 get /kafka/consumers/（消费者组）/offsets/（主题）/（分区）

下面看一下消费者的配置

```
# 是否自动提交  如果设置成false需要手动提交
auto.commit.enable = true
# 自动提交的时间间隔
auto.commit.interval.ms = 60 * 1000
```
 
如果是设置自动提交，会在 consumer.poll()方法之后自动提交偏移量，这种情况可能导致在取得数据之后，自动提交了偏移量，然后在应用中处理业务的时候事物失败了，导致丢失数据，offset值又加1了，那么下次消费的时候就丢失了数据，如果对于允许丢失的数据，比如日志生成环境N多个集群对接的日志服务系统，使用kafka作为消息中间件，日志这种可以允许丢失的话设置为自动提交无所谓，如果对于数据一定不能丢失，这里需要配置为false，调用kafka提供的API手工提交

虽然可以设置为手工提交，但是好像并不能保证数据库事物与offset一起提交成功，因为 手工提交之后offset变了，但是最后面执行数据库事物操作异常了，这种情况下又感觉消费者数据丢失了，那么这种情况下如何保证消费者不重复消费数据或者，一定保证提交偏移量的数据消费了？

下面有一种办法，就是把主题，分区，消费者组，offset这些关系存到数据库当中，消费者每次取数据的时候设置一下偏移量，调用消费的seek()方法，这些API可以具体参考kafka的文档，让保存偏移量和应用的事物保持一致就行，具体需不需要这些场景根据业务情况而定

---

## Data Lake

- A data lake is a system or repository of data stored in its natural format, usually object blobs or files. 
- A data lake is usually a single store of all enterprise data including raw copies of source system data and transformed data used for tasks such as reporting, visualization, analytics and machine learning. 
- A data lake can include structured data from relational databases (rows and columns), semi-structured data (CSV, logs, XML, JSON), unstructured data (emails, documents, PDFs) and binary data (images, audio, video). 

Examples:

- Azure Data Lake 
- HDFS
- S3


---

## Data Skew

[Spark性能优化之道——解决Spark数据倾斜（Data Skew）的N种姿势](https://zhuanlan.zhihu.com/p/31393452)

[什么是数据倾斜?如何解决数据倾斜? ](https://www.sohu.com/a/224276626_543508)

[数据倾斜及其高效解决方法](https://blog.csdn.net/anshuai_aw1/article/details/84033160)

---

## 大数查找

### 100亿数据找出最大的1000个数字（top K问题）

在大规模数据处理中，经常会遇到的一类问题：在海量数据中找出出现频率最好的前k个数，或者从海量数据中找出最大的前k个数，这类问题通常被称为top K问题。例如，在搜索引擎中，统计搜索最热门的10个查询词；在歌曲库中统计下载最高的前10首歌等。

1、最容易想到的方法是将数据全部排序。该方法并不高效，因为题目的目的是寻找出最大的10000个数即可，而排序却是将所有的元素都排序了，做了很多的无用功。

2、局部淘汰法。用一个容器保存前10000个数，然后将剩余的所有数字一一与容器内的最小数字相比，如果所有后续的元素都比容器内的10000个数还小，那么容器内这个10000个数就是最大10000个数。如果某一后续元素比容器内最小数字大，则删掉容器内最小元素，并将该元素插入容器，最后遍历完这1亿个数，得到的结果容器中保存的数即为最终结果了。此时的时间复杂度为O（n+m^2），其中m为容器的大小。

这个容器可以用（小顶堆）最小堆来实现。我们知道完全二叉树有几个非常重要的特性，就是假如该二叉树中总共有N个节点，那么该二叉树的深度就是log2N，对于小顶堆来说移动根元素到 底部或者移动底部元素到根部只需要log2N，相比N来说时间复杂度优化太多了（1亿的logN值是26-27的一个浮点数）。基本的思路就是先从文件中取出1000个元素构建一个小顶堆数组k，然后依次对剩下的100亿-1000个数字进行遍历m，如果m大于小顶堆的根元素，即k[0]，那么用m取代k[0]，对新的数组进行重新构建组成一个新的小顶堆。这个算法的时间复杂度是O((100亿-1000)log(1000))，即O((N-M)logM)，空间复杂度是M

这个算法优点是性能尚可，空间复杂度低，IO读取比较频繁，对系统压力大。

3、第三种方法是分治法，即大数据里最常用的MapReduce。

a、将100亿个数据分为1000个大分区，每个区1000万个数据

b、每个大分区再细分成100个小分区。总共就有1000*100=10万个分区

c、计算每个小分区上最大的1000个数。

为什么要找出每个分区上最大的1000个数？举个例子说明，全校高一有100个班，我想找出全校前10名的同学，很傻的办法就是，把高一100个班的同学成绩都取出来，作比较，这个比较数据量太大了。应该很容易想到，班里的第11名，不可能是全校的前10名。也就是说，不是班里的前10名，就不可能是全校的前10名。因此，只需要把每个班里的前10取出来，作比较就行了，这样比较的数据量就大大地减少了。我们要找的是100亿中的最大1000个数，所以每个分区中的第1001个数一定不可能是所有数据中的前1000个。

d、合并每个大分区细分出来的小分区。每个大分区有100个小分区，我们已经找出了每个小分区的前1000个数。将这100个分区的1000*100个数合并，找出每个大分区的前1000个数。

e、合并大分区。我们有1000个大分区，上一步已找出每个大分区的前1000个数。我们将这1000*1000个数合并，找出前1000.这1000个数就是所有数据中最大的1000个数。

（a、b、c为map阶段，d、e为reduce阶段）

4、Hash法。如果这1亿个数里面有很多重复的数，先通过Hash法，把这1亿个数字去重复，这样如果重复率很高的话，会减少很大的内存用量，从而缩小运算空间，然后通过分治法或最小堆法查找最大的10000个数。

**对于海量数据处理，思路基本上是：必须分块处理，然后再合并起来。**