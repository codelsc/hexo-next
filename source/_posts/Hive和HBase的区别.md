---
title: Hive和HBase的区别
date: 2015-04-12 09:32:16
categories:
 - Hadoop
tags:
 - Hive
 - HBase
---
**Hive**是为了简化编写MapReduce程序而生的，使用MapReduce做过数据分析的人都知道，很多分析程序除业务逻辑不同外，程序流程基本一样。在这种情况下，就需要Hive这样的用戶编程接口。Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑，就是些表的定义等，也就是表的元数据。使用SQL实现Hive是因为SQL大家都熟悉，转换成本低，类似作用的Pig就不是SQL。
<!-- more -->
**Hbase**为查询而生的，它通过组织起节点內所有机器的內存，提供一個超大的內存Hash表，它需要组织自己的数据结构，包括磁盘和內存中的，而Hive是不做这个的，表在HBase中是物理表，而不是逻辑表，搜索引擎使用它來存储索引，以满足查询的实时性需求。
>hive类似CloudBase，也是基于Hadoop分布式计算平台上的提供data warehouse的sql功能的一套软件。使得存储在hadoop里面的海量数据的汇总，即席查询简单化。hive提供了一套QL的查询语言，以sql为基础，使用起来很方便。

>HBase是一个分布式的基于列存储的非关系型数据库。HBase的查询效率很高，主要由于查询和展示结果。 
hive是分布式的关系型数据库。主要用来并行分布式处理大量数据。hive中的所有查询除了"select * from table;"都是需要通过Map\Reduce的方式来执行的。由于要走Map\Reduce，即使一个只有1行1列的表，如果不是通过select * from table;方式来查询的，可能也需要8、9秒。但hive比较擅长处理大量数据。当要处理的数据很多，并且Hadoop集群有足够的规模，这时就能体现出它的优势。

通过hive的存储接口，hive和Hbase可以整合使用。

- 1、hive是sql语言，通过数据库的方式来操作hdfs文件系统，为了简化编程，底层计算方式为mapreduce。
- 2、hive是面向行存储的数据库。 
- 3、Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑。
- 4、HBase为查询而生的，它通过组织起节点內所有机器的內存，提供一個超大的內存Hash表 。
- 5、hbase不是关系型数据库，而是一个在hdfs上开发的面向列的分布式数据库，不支持sql。 
- 6、hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作。
- 7、hbase是列存储。

Hive和Hbase有各自不同的特征：hive是高延迟、结构化和面向分析的，hbase是低延迟、非结构化和面向编程的。Hive数据仓库在hadoop上是高延迟的。 
其中HBase位于结构化存储层，Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供了高性能的计算能力，Zookeeper为HBase提供了稳定服务和failover机制。
