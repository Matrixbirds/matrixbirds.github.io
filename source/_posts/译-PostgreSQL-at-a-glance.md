title: 译-postgresql-at-a-glance
date: 2016-04-16 11:47:44
tags:
  - 翻译
  - PostgreSQL
---

翻译[postgresql-at-a-glance](http://www.cubrid.org/blog/dev-platform/postgresql-at-a-glance)

PostgreSQL展示了它丰富的功能以及不错的性能。考虑到它的质量高，但奇怪的是PostgreSQL并没有流行起来。
这并不影响到它的前进。本文讲安利一下它的发展史以及特性。

#### 为什么你需要了解PostgreSQL

PostgreSQL 是一个关系型数据库管理系统，在北美和11区相当的流行。在棒子国并不是很流行，但它依然是个具备强大功能
和卓越性能的一个优秀的关系型数据库，它是值得去研究和学习的。

PostgreSQL (读音为：[Post-Gres-Q-L])是一个对象关系型数据库。并且它还是一个开源代码的数据库，提供了企业级
的关系型数据库具备的功能。PostgreSQL作为开源数据库具有与Oracle一样的知名度，也有和Oracle相似的功能。

#### 发展史

PostgreSQL 由许多先驱数据库演变而来。Ingres是PostgreSQL的鼻祖。
[Michael Stonebraker](https://en.wikipedia.org/wiki/Michael_Stonebraker)是Ingres数据库的发起人。
他是数据库领域的大师，至今都在努力研究数据库领域。
Ingres这个项目是在1997年，伯克利大学发起的。在Ingres启动后Michael Stonebraker开始了另一个项目，Postgres(Post-Ingres)。
1991年发布了Postgres第三版，它的用户群体变的非常庞大。但对随着用户提供支持的负担太高，在1993年项目被叫停了。
（Postgres对当时的Informix产品产生了巨大的影响，甚至在项目被叫停的时候。）

尽管项目被叫停了。使用Postgres的用户和学生他们借着当前的版本创建了一个叫做Postgres95的项目，并且提升了
40%的性能，还提供了比Postgres更强大的SQL支持并且改善了数据结构。

在1996年Postgres95开发了源码，并且改名为PostgreSQL，也就是它现在的名字。这个名字体现出它Postgres的成功以及对SQL的支持。
（早期PostgreSQL采用的是QUEL语言，而不是SQL语言）。1997年，PostgresSQL发布了更名之后的第一个版本6.0

从那时起，PostgreSQL在开源社区的发展下，到了2013年5月的9.2版本。此外它的开源许可类似BSD和MIT许可。
PostgreSQL允许商业使用和修改，但是许可也声明源开发者并不承担任何发生在使用中的法律责任。PostgresSQL超过20多种不同版本的
fork。其中一些影响PostgresSQL，一些则已经消失。

PostgresSQL的图标是一个叫做`Slonik`的大象（这是来源于俄语，大象宝宝的意思）。然而并没有准确的理由可以证明为何使用大象作为图标，
有人说是在它开放源码后，它的用户从[Agatha Christie's](https://en.wikipedia.org/wiki/Agatha_Christie)的[小说](https://en.wikipedia.org/wiki/Elephants_Can_Remember)中受到启发，并且提议使用大象作为图标。从那以后大象标志作为PostgresSQL
官方的图标。

大象还象征着庞大，强大，可靠和有好的记忆，Hadoop和Evernode也是用大象作为官方图标。

#### 功能性以及局限性

PostgresSQL提供事务的支持和ACID，这都是作为关系型数据最基本的功能。此外PostgresSQL还支持许多先进的功能和扩展功能对于
学术研究提供了基本的可靠性和稳定性。PostgresSQL具有相当丰富的功能列表。
  * 嵌套的事务（savepoints)
  * 时间恢复点
  * 在线热备份，并行还原
  * 规则系统（query write system)
  * B-tree, R-tree, hash, GiST 方法索引
  * 多版本并发控制（MVCC）
  * 表空间
  * Procedural Language
  * Information Schema
  * I18n, L10N（国际化，本地化）
  * 列级排序规则
  * 数组，XML，UUID（数据类型）
  * 序列（用于值的自增）
  * 异步复制
  * limit/offset
  * 全文检索
  * SSL, IPv6
  * key-value（键值） 存储
  * 表继承

除了这些以外，它还具有多种功能和企业级数据库的功能。
总体上来看，PostgresSQL还存在以下的许多存储限制：  

| Limit | Value |
| :---: | :-----: |
| 数据库存储最大上限 | 无限制 |
| 单表最大存储上限 | 32TB |
| 单表行最大存储上限 | 1.6GB |
| 字段最大存储上限 | 1GB |
| 单表行数上限 | 无限制 |
| 单表列数上限 | 250~1600 |
| 单表最索引数 | 无限制 |

#### PostgresSQL里程碑

| 版本 | 发布时间 | 主要功能 |
| :--: | :-----: | :-----: |
| 0.01 | 1995 | Postgres95 发布 |
| 1.0  | 1995 | 版权变动，开放源码|
| 6.0~6.5 | 1997~1999 | 更名 PostgreSQL，加入索引、试图、规则，序列，触发器，查询优化器，约束，子查询，MVCC和JDBC接口 |
| 7.0~7.4 | 2000~2010 | 外键，SQL92语法加入，日志预写，Information schema和国际化 |
| 8.0~8.4 | 2005~2012 | 提供windows平台原生支持，Savepoint, Point-in-time，recovery，Two-phase commit，表空间, 切分，全文检索，Common table expressions (CTE)，SQL/XML, ENUM, UUID Type，Window functions，Per-database collation，复制，备份 |
| 9.0 | 2010-09 | 流式复制, 热备份，提供64位windows系统软件支持，基于列的触发器
| 9.1 | 2011-09 | 功能分化，同步复制，列排序，Unlogged 表，K-nearest-neighbor 索引，序列化隔离级别，Writeable CTE (WITH)，SQL/MED External Data，SE-Linux的集成
| 9.2 | 2012-09 | 性能优化，linear scalability to 64 cores，降低CPU层面的耗能，Cascade 流式复制，JSON, Range 数据类型，优化了锁的管理，Space-partitioned GiST index，索引扫描（覆盖）

下一个PostgreSQL版本是9.3，在2013年第三季发布。此版本具有许多功能，包括增强的管理功能：并行查询，合并、更改，插入，多版本主复制，物理化试图，增强多语言支持。

#### 内部结构
以下是PostgreSQL的内部构造：  
![图片木有加载出来](https://raw.githubusercontent.com/Matrixbirds/matrixbirds.github.io/deployer/pictures/postgresql_process_structure.png)

当客户端请求和服务器建立连接，通过步骤（1）接口库（接口包括，libpg，JDBC和ODBC）。
PostMaster程序通过步骤（2）和服务器传播，然后客户端通过和所分配的服务器连接，执行查询。  

以下是PostgreSQL服务的查询过程：
![图片木有加载出来](https://raw.githubusercontent.com/Matrixbirds/matrixbirds.github.io/deployer/pictures/postgresql_query_execution_procedure.png)
当服务器收到客户发来的查询请求时，系统会创建一个语法解析树，系统会根据创建的语法解析树（1）分析，创建一个查询树。  
接着查询树根据服务器定义的规则（3）找到多个可执行的计划，最终会根据最优的计划生成查询树。  
当服务器执行查询时候，数据库系统里的catalog使用频繁，在系统内置的catalog中，用户可以直接定义函数和数据结构。以及索引访问方法和规则。在PostgreSQL里catalog起着很重要的作用，用于增加和扩展它的功能。

在PostgreSQL里一个文件可以由多个文件构成，一个页面有一个可扩展开槽页结构：
![图片木有加载出来](https://raw.githubusercontent.com/Matrixbirds/matrixbirds.github.io/deployer/pictures/postgresql_data_page_structure.png)
![图片木有加载出来](https://raw.githubusercontent.com/Matrixbirds/matrixbirds.github.io/deployer/pictures/postgresql_index_page_structure.png)

#### 未完待续...
