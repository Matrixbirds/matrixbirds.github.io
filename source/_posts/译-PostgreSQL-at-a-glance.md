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
学术研究提供了基础可靠性和稳定性。
