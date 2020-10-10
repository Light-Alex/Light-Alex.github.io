---
title: Redis学习笔记
date: 2020-08-26 21:55:33
tags: ['数据库', 'Redis']
category: ['数据库']
mathjax: true
typora-root-url: ..
---

# 什么是NoSQL

NoSQL = Not Only SQL （不仅仅是SQL）

关系型数据库：表格，行，列

泛指非关系型数据库，随着web2.0互联网的诞生！传统的关系型数据库很难对付web2.0时代！尤其是超大规模的高并发的社区！暴露出很多难以克服的问题，NoSQL在当今大数据环境下发展十分迅速，Redis是发展最快的，是我们当下必须要掌握的技术！

很多的数据类型用户的个人信息，社交网络，地理位置，这些数据类型的存储不需要一个固定的格式！不需要多余的操作就可以横向扩展！Map<String, Object> 使用键值对控制！



<!--more-->



# NoSQL特点

解耦！

1、方便扩展（数据之间没有关系，很好扩展！）

2、大数据量高性能（Redis一秒写8万次，读取11万次，NoSQL的缓存，记录级，是一种细粒度的缓存，性能会比较高！）

3、数据类型是多样型的！（不需要事先设计数据库！随取随用！如果是数据库量十分大，就不好设计数据库了）

4、传统的RDBMS和NoSQL

```
传统的 RDBMS
- 结构化组织
- SQL
- 数据和关系都存在单独的表中 row col
- 数据操作，数据定义语言
- 严格的一致性
- 基础的事务
- ......
```

```
NoSQL
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库（社交关系）
- 最终一致性
- CAP理论 和 BASE理论（异地多活）
- 高性能、高可用、高可扩展性
- ......
```

> 了解：3V+3高

大数据时代的3V：主要是描述问题的

1. 海量Volume
2. 多样Variety
3. 实时Velocity

大数据时代的3高：主要对程序的要求

1. 高并发
2. 高可扩（随时水平拆分，机器不够了，可以扩展机器数量）
3. 高性能（保证用户体验和性能！）



在公司中实践：NoSQL + RDBMS



# 数据存储

```bash
# 1、商品的基本信息
	名称、价格、商家信息
	关系型数据库就可以解决！MySQL / Oracle （淘宝早年就去IOE了！-王坚：推荐文章：阿里云的这群疯子）
	淘宝内部的MySQL不是大家用的MySQL

# 2、商品的描述、评论（文字比较多）
	文档型数据库中：MongoDB
	
# 3、图片
	分布式文件系统：FastDFS
	- 淘宝       TFS
	- Google的  GFS
	- Hadoop    HDFS
	- 阿里云     oss
	
# 4、商品关键字（搜索）
	- 搜索引擎 solr elasticsearch
    - ISearch：多隆（阿里技术大佬）

# 5、商品热门的波段信息
	- 内存数据库
	- Redis、Tair、Memcache...

# 6、商品的交易，外部的支付接口
	- 三方应用
```



# NoSQL的四大分类

**KV键值对：**

* 新浪：Redis
* 美团：Redis + Tair
* 阿里、百度：Redis + memcache

**文档型数据库（bson格式和json一样）：**

* MongoDB
  * MongoDB是一个基于分布式文件存储的数据库，C++编写，主要用来处理大量的文档！
  * MongoDB是一个介于关系型数据库和非关系型数据库中间的产品！MongoDB是非关系型数据库中功能最丰富，最像关系型数据库的！
* ConthDB

**列存储数据库：**

* HBase
* 分布式文件系统

**图关系数据库：**

* 他不是存图形的，放的是关系，比如：朋友圈社交网络，广告推荐！
* Neo4j
* InfoGrid



**四者对比：**

| 分类                  | Example举例                                        | 典型应用场景                                                 | 数据模型                                     | 优点                                                         | 缺点                                                         |
| :-------------------- | -------------------------------------------------- | ------------------------------------------------------------ | -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **键值（key-value）** | Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。 | Key指向Value的键值对，通常用hash table来实现 | 查找速度快                                                   | 数据无结构化，通常只被当作字符串或者二进制数据               |
| **列存储数据库**      | Cassandra, HBase, Rlak                             | 分布式文件系统                                               | 以列簇式存储，将同一列数据存在一起           | 查找速度快，可扩展性强，更容易进行分布式扩展                 | 功能相对局限                                                 |
| **文档型数据库**      | CouchDB, MongoDB                                   | Web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解的Value的内容） | Key-Value对应键值对，Value为结构化数据       | 数据结构要求严格，表结构可变，不需要像关系型数据库一样预先定义表结构 | 查询性能不高，而且缺乏统一的查询语法                         |
| **图形(Graph)数据库** | Neo4j, InfoGrid, Infinite Graph                    | 社交网络，推荐系统等。专注于构建关系图谱                     | 图结构                                       | 利用图结构相关算法。比如最短路径寻址，N度关系查找            | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群方案 |



# Redis入门

## 概述

> Redis是什么？

Redis（**Re**mote **Di**ctionary **S**erver )，即远程字典服务，是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

![Redis支持的语言](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/Redis%E6%94%AF%E6%8C%81%E7%9A%84%E8%AF%AD%E8%A8%80.png)

> Redis能做什么？

1、内存存储、持久化，内存是断电即失的，所以持久化很重要（RDB、AOF）

2、效率高，可以用于高速缓存

3、发布订阅系统

4、地图信息分析

5、计时器、计数器（浏览量！）

6、......

> 特性

1、多样的数据类型

2、持久化

3、集群

4、事务

5、......

> 学习中需要用到的东西

1、官网：https://redis.io/

2、中文网：http://www.redis.cn/

![redis介绍](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E4%BB%8B%E7%BB%8D.png)

3、下载地址：通过官网下载即可！

注意：Windows在GitHub上下载（停更很久了）

Redis推荐在Linux服务器上搭建



## Windows安装

### 下载压缩包：

https://github.com/MicrosoftArchive/redis/tags

![redis压缩包](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E5%8E%8B%E7%BC%A9%E5%8C%85.png)

### 解压到电脑的环境目录下

![环境目录](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%8E%AF%E5%A2%83%E7%9B%AE%E5%BD%95.png)

![redis目录](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E7%9B%AE%E5%BD%95.png)

### 开启Redis，双击运行redis-server.exe

![双击运行redis服务](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%8F%8C%E5%87%BB%E8%BF%90%E8%A1%8Credis%E6%9C%8D%E5%8A%A1.png)

### 使用redis客户端连接redis，打开redis-cli.exe

![打开redis客户端](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%89%93%E5%BC%80redis%E5%AE%A2%E6%88%B7%E7%AB%AF.png)

### 简单测试

![简单测试](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%AE%80%E5%8D%95%E6%B5%8B%E8%AF%95.png)

> Windows下使用很简单，但是Redis推荐我们使用Linux去开发使用！

![redis简介](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E7%AE%80%E4%BB%8B.png)

![redis简介中文版](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E7%AE%80%E4%BB%8B%E4%B8%AD%E6%96%87%E7%89%88.png)



## Linux安装

### 查看可用的 Redis 版本

dockerhub网址：https://hub.docker.com/_/redis?tab=tags

![dockerhub](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/dockerhub.png)

### 取最新版的 Redis 镜像

```bash
docker pull redis:6.0.6
```

![拉取镜像](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%8B%89%E5%8F%96%E9%95%9C%E5%83%8F.png)

### 查看本地镜像

使用以下命令来查看是否已安装了 redis：

```bash
docker images | grep redis
```

![查看镜像](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%9F%A5%E7%9C%8B%E9%95%9C%E5%83%8F.png)

### 运行容器，并做好端口映射

安装完成后，我们可以使用以下命令来运行 redis 容器：

```bash
docker run -itd --name redis_ycx -p 46379:6379 -p 46322:22 redis:6.0.6 /bin/bash
```

参数说明：

>  -p 46379:6379: 映射容器服务的 6379 端口到宿主机的 46379端口。外部可以直接通过宿主机ip:46379访问到 Redis 的服务。

![运行镜像](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%BF%90%E8%A1%8C%E9%95%9C%E5%83%8F.png)

### 安装成功

我们可以通过 **docker ps** 命令查看容器的运行信息：

```bash
docker ps | grep redis_ycx
```

![查看容器运行信息](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%9F%A5%E7%9C%8B%E5%AE%B9%E5%99%A8%E8%BF%90%E8%A1%8C%E4%BF%A1%E6%81%AF.png)

### 进入容器

```bash
docker exec -it redis_ycx /bin/bash
```

![进入容器](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%BF%9B%E5%85%A5%E5%AE%B9%E5%99%A8.png)

### 设置root用户密码

```bash
passwd root
```

### 更新apt

```bash
apt-get update
```

### 安装vim

```bash
apt-get install vim
```

### ssh permission denied问题解决

修改/etc/ssh/sshd_config文件

```bash
vim /etc/ssh/sshd_config
```

将相应行**PermitRootLogin **改为***PermitRootLogin yes**

![修改sshd_config文件](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%BF%AE%E6%94%B9sshd_config%E6%96%87%E4%BB%B6.png)

### 安装SSH server

> Ubuntu 默认已安装了 SSH client

```bash
apt-get install openssh-server
```

### 启动SSH服务

```bash
service ssh start
```

### 使用如下命令登陆本机

```bash
ssh localhost
```

此时会有如下提示(SSH首次登陆提示)，输入 yes 。然后按提示输入密码，这样就登陆到本机了。

### 免密登录

```bash
cd ~/.ssh/                     # 若没有该目录，请先执行一次ssh localhost
ssh-keygen -t rsa              # 会有提示，都按回车就可以
cat id_rsa.pub >> authorized_keys  # 加入授权
chmod 600 ./authorized_keys    # 修改文件权限
```

### 使用shereis命名查看redis安装目录

```bash
whereis redis-cli
```

![redis安装目录](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E5%AE%89%E8%A3%85%E7%9B%AE%E5%BD%95.png)

### 创建自己的redis配置文件

```bash
cd /usr/local/bin
mkdir myredisconifg # 创建myredisconifg目录
vim redis.conf # 创建配置文件
```

去github查看redis.conf的内容（docker默认是没有配置的），将内容复制到redis.conf文件中

### redis默认不是后台启动的，需要修改配置文件redis.conf

```bash
vim redis.conf
/daemonize # 在vim中查找daemonize文本
daemonize yes # 允许后台启动，以守护进程的方式启动
```

### 启动redis服务

```bash
cd /usr/local/bin
redis-server myredisconifg/redis.conf # 通过指定的配置文件启动服务
```

### 测试redis

```bash
cd /usr/local/bin
redis-cli -p 6379 # 使用redis客户端测试连接，指定端口号，默认6379
ping # 测试是否连通
set name yan # 设置key value
get name # 获取指定key的value
keys * # 查看所有的key
```

![测试redis](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%B5%8B%E8%AF%95redis.png)

### 查看redis进程是否开启

```bash
ps -ef | grep redis
```

### 如何关闭redis服务

```bash
kill -9 进程号 # 上一步查出的redis进程号
```



## 测试性能

redis-benchmark是一个压力测试工具

官方自带的性能测试工具

redis-benchmark命令参数：

![benchmark参数](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/benchmark%E5%8F%82%E6%95%B0.png)

简单测试：

```bash
# 测试：100个并发连接，100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

![压力测试结果](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%9C.png)



## redis-cli

```bash
redis-cli -h host -p port -a password
```



## 基础知识

redis默认有16个数据库

![默认16个数据库](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%BB%98%E8%AE%A416%E4%B8%AA%E6%95%B0%E6%8D%AE%E5%BA%93.png)

默认使用的是第0个

可以使用select进行切换

```bash
select 3 # 切换到3号数据库
dbsize # 查看数据库大小
set key value # 设置key，value
get key # 根据key获取对应的value
keys * # 查看当前数据库所有的key
flushdb # 清空当前数据库
flushall # 清空所有数据库
```

思考：为什么redis是6379？（女明星MERZ手机9宫格输入法对应数字6379）



> Redis是单线程的！

Redis是基于内存操作的，CPU不是Redis的性能瓶颈，Redis的瓶颈是机器的内存和网络带宽。

Redis是C语言写的，官方提供的数据为100000+的QPS，完全不比同样是使用key-value的Memecache差

**Redis为什么单线程这么快？**

误区1：高性能的服务器一定是多线程的？

误区2：多线程（CPU上下文切换！）一定比单线程效率高？

速度：CPU>内存>硬盘

核心：Redis是将所有的数据全部放在内存中的，所以使用单线程去操作效率高，多线程（CPU上下文切换是耗时的操作），对于内存系统来说，如果没有上下文切换，效率就是最高的，多次读写都是在一个CPU上，在内存情况下，这个就是最佳的方案。（IO多路复用）



# 五大基本数据类型

![5大基本数据类型](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/5%E5%A4%A7%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B.png)

**翻译：**

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。



## Redis-Key

```bash
select 3 # 切换到3号数据库
dbsize # 查看数据库大小
set key value # 设置key，value
get key # 根据key获取对应的value
flushdb # 清空当前数据库
flushall # 清空所有数据库
keys * # 查看所有的key
exists name # 判断是否有值为 name 的key
move name 1 # 将 name 移动到1号数据库
del name # 删除值为 name 的key
expire name 10 # 设置 name 的过期时间(单位s)
ttl name # 查看当前 name 的过期时间
type name # 查看 name 的类型
```

参数查询：http://www.redis.cn/commands.html



## String（字符串）

```bash
#################################################################
set key1 v1
get key1 # v1
append key1 hello # 在key1对应的value后追加字符串hello，如果当前key不存在，就相当于set key
get key1 # v1hello
strlen key1 # 查看 key1 对应的value的字符串长度
#################################################################
set views 0 # 初始浏览量为0
incr views # views的值+1
decr views # views的值-1
incrby views 10 # views的值+10
decrby views 5 # views的值-5
#################################################################
# 字符串范围 range
set key1 hello,world!
getrange key1 0 3 # 截取字符串，范围：0-3，结果：hell
getrange key1 0 -1 # 截取全部字符串，效果等同与get key1，结果：hello,world!

# 替换字符串
set key2 abcdefg
setrange key2 1 xx # 从位置1开始替换字符串，结果：axxdefg
#################################################################
# setex (set with expire)  # 设置key并设置过期时间
# setnx (set if not exist) # key不存在才设置，在分布式锁中会常常使用！
setex key3 30 hello2 # 设置key3的值为hello2，过期时间为30s
setnx mykey redis # mykey不存在，才设置mykey，值为redis，如果mykey存在，就会设置失败，返回0
#################################################################
# mset # 批量设置key的value
# mget # 批量获取key的value
# msetnx # 如果不存在，就设置，原子性操作，其中有一个key存在，就都不会设置成功
mset k1 v1 k2 v2 k3 v3 # 设置k1的value为v1，k2的value为v2，k3的value为v3
mget k1 k2 k3 # 获取k1,k2,k3的值，结果：v1,v2,v3
msetnx k1 a1 k2 a2 k4 v4 # k1, k2存在，所以全部设置失败，k4也不会设置
get k4 # (nil)
# 对象
set user:1 {name:zhangsan,age:3} # 设置一个user:1对象，值为json字符串，用来保存一个对象
mset user:1:name zhangsan user:1:age 2 # 方式二，这里的key：user:{id}:{field}
mget user:1:name user:1:age # 1) "zhangsan" 2) "2"
#################################################################
# getset # 先get再set
getset db redis # 返回(nil)
get db # 再次获取，值为redis
```

**String的使用场景：**value除了字符串还可以是数字！

* 计数器
* 统计多单位的数量
* 粉丝数
* 对象缓存存储



## List

> 基本的数据类型，列表

在redis里面，可以把list玩成：栈、队列、阻塞队列！

**redis不区分大小写命令**

所有的list命令都是用l或r开头的，可以理解为**双端队列**

```bash
#################################################################
lpush list one # 将一个值或多个值，从左侧放入列表
lpush list two three
lrange list 0 -1 # 从左开始，获取列表的所有值，three two one
rpush list four # 将一个值或多个值，从右侧放入列表
lrange list 0 -1 # 从左开始，获取列表的所有值，three two one four
lrange list 0 1 # 从左开始，获取列表的第0个到第1个值，three two
#################################################################
# lpop # 从列表左侧移除第一个元素，返回该元素
# rpop # 从列表右侧移除第一个元素，返回该元素
lpop list # 从列表左侧移除第一个元素，返回three
rpop list # 从列表右侧移除第一个元素，返回four
lrange list 0 -1 # two one
#################################################################
# lindex 从左侧，通过下标获取某一个值
lindex list 0 # 获取下标为0的值，two
#################################################################
# llen 获取列表的长度
llen list # 2
#################################################################
# lrem: 移除list列表中，指定个数的value，精确匹配
lrem list 2 three # 从左侧开始，移除两个值为three的元素
#################################################################
# ltrim: 截取指定范围的元素
rpush list hello1 hello2 hello3 hello4
lrange list 0 -1 # hello1 hello2 hello3 hello4
ltrim list 1 2 # 截取[1,2]范围的值
lrange list 0 -1 # hello2 hello3
#################################################################
# rpoplpush：将列表右侧第一个元素，从左侧移动到另一个列表中
rpush list hello1 hello2 hello3 hello4
lrange list 0 -1 # hello1 hello2 hello3 hello4
rpoplpush list mylist # 返回：hello4
lrange list 0 -1 # hello1 hello2 hello3
lrange mylist 0 -1 # hello4
#################################################################
# lset：通过下标赋值，前提是列表存在，且下标没有越界
rpush list asd # asd
lset list 0 a1 # 将下标为0的值设置为a1（更新操作）
#################################################################
# linsert: 在某个值的前面或后面插入一个值（从左开始找指定值，然后在其前面或后面插入一个值）
rpush list h1 h2 h3 h4 h2 # h1 h2 h3 h4 h2
linsert list before h2 h8 # 从左开始，在h2前面插入h8，结果：h1 h8 h2 h3 h4 h2
```

**小结：**

* list底层是双向链表
* 如果key不存在，创建新链表
* 如果key存在，新增内容
* 如果移除了所有值，空链表，代表不存在
* 在两边插入或者改动，效率最高！操作中间元素，效率相对较低
* 队列：lpush rpop
* 栈：lpush lpop



## Set（集合）

> set中的值是不重复的，无序的

```bash
#################################################################
sadd myset hello # myset集合中添加一个值hello
smembers myset # 查看myset中的所有元素
sismember myset hello # 查看myset集合中是否包含值hello
#################################################################
scard myset # 查看myset集合的元素个数
#################################################################
srem myset hello # 移除myset集合中的hello元素
#################################################################
srandmember myset # 随机抽选出myset集合中的1个元素
srandmember myset 2 # 随机抽选出myset集合中的2个元素
#################################################################
# 随机移除集合中的一个元素
spop myset # 随机移除myset集合中的一个元素，返回该元素: hello
#################################################################
# 将一个指定的值移动到另外一个set集合中
smove myset myset2 hello # 将myset集合中的hello元素移动到myset2集合中
#################################################################
# 交集，并集，差集，补集
sadd key1 a b c # a b c
sadd key1 c d e # c d e
sdiff key1 key2 # key1集合和key2集合做差集，返回key1集合中除开key2集合的那部分：a b
sinter key1 key2 # key1集合和key2集合做交集，返回c
sunion key1 key2 # key1集合和key2集合做并集，返回a b c d e
# 补集可由差集和交集实现
```

**应用场景：**

微博，A用户将所有关注的人放入一个set集合中，将他的粉丝也放入一个集合中！

交集：共同关注、共同爱好、二度好友、推荐好友！（六度分隔理论）

**补充（Java实现交并差）：**

> 下面的方法会改变A集合

交集：A.retainAll(B) 

并集：A.addAll(B)

差集：A.removeAll(B)



## Hash(哈希)

Map集合，key-value

```bash
#################################################################
hset myhash field1 xiaoqiang field2 hello # 添加一个或多个key-value
hget myhash field1 # 获取myhash中field1的value值
hmset myhash field1 xiaoqiang field2 hello field3 world # 添加一个或多个key-value，效果和hset一样（相同key的value会被更新）
hmget myhash field1 field2 # 批量获取多个key的value值
hgetall myhash # 获取myhash中的所有key和value
hdel myhash field1 # 删除myhash中key为field1的键值对，可以同时删除多个
#################################################################
hlen myhash # 获取myhash的长度
#################################################################
hexists myhash field1 # 判断myhash中key为field1的字段是否存在
#################################################################
hkeys myhash # 获取myhash中所有的key
hvals myhash # 获取myhash中所有的value
#################################################################
hincrby myhash field1 3  # key为field1的value自增3
hincrby myhash field1 -3 # key为field1的value自减3
hsetnx myhash field4 hello # myhash中若不存在key为field4的键值对，则会添加该键值对，否则添加失败
```

**hash更适合对象的存储！**

**String更适合字符串存储！**



## Zset(有序集合)

在Set的基础上，增加了一个值，用于排序，zadd 集合名 用于排序的score 元素

```bash
#################################################################
zadd myset 1 one         # 添加一个值
zadd myset 2 two 3 three # 添加多个值
zrange myset 0 -1 # 获取myset集合中的所有值，one two three
#################################################################
# 排序如何实现
# zrangebyscore key 最小值 最大值
zadd myset 2500 xiaoming 5000 zhangsan 500 kuang # 添加3个用户
zrangebyscore salary -inf +inf # 显示全部的用户，根据score，从小到大排序
zrangebyscore salary -inf 2500 # 只排序score为 -inf到2500 的元素，从小到大排序
zrangebyscore salary -inf 2500 withscores # 排序结果带上score kuang 500 xiaoming 2500
zrevrangebyscore salary +inf -inf # 根据score，从大到小排序，排序所有用户
zrevrange salary 0 -1 # 从大到小进行排序
#################################################################
# 移除元素
zrem salary xiaoming kuang # 移除一个或多个元素
#################################################################
zcard salary # 获取salary有序集合中有多少个元素
#################################################################
zadd myset 1 hello 2 world 3 kuang
zcount myset 1 3 # 获取score在[1,3]区间有多少个元素，返回：3
```

其余的一些api，可以查看官方文档。

案例思路：set 排序 存储班级成绩表，工资表排序

普通消息，1，重要信息，2，带权重进行判断

排行榜应用实现，取Top N 测试



# 三种特殊数据类型

## geospatial地址位置

> 朋友的定位，附近的人，打车距离计算

Redis的Geo在Redis3.2版本就推出了！这个功能可以推算地理位置的信息，两地之间的距离，方圆几里的人！

可以查询一些测试数据：http://www.jsons.cn/lngcode/

只有6个命令

![6个地理位置命令](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/6%E4%B8%AA%E5%9C%B0%E7%90%86%E4%BD%8D%E7%BD%AE%E5%91%BD%E4%BB%A4.png)

> geoadd 添加地理位置
>
> geoadd key longitude latitude member [longitude latitude member ...]

```bash
# geoadd 添加地理位置
# geoadd key 经度 维度 名称
# 有效的经度从-180度到180度。
# 有效的纬度从-85.05112878度到85.05112878度。
# 当坐标位置超出上述指定范围时，该命令将会返回一个错误。
# 规则：两级无法直接添加，我们一般会下载城市数据，直接通过Java程序一次性导入！
geoadd china:city 116.405285 39.904989 beijing 121.472644 31.231706 shanghai # 可以一次性导入多个值，添加了北京和上海的地理信息
geoadd china:city 106.504962 29.533155 chongqing #添加重庆的地理信息
geoadd china:city 114.085947 22.547 shenzhen # 添加深圳的地理信息
geoadd china:city 120.153576 30.287459 hangzhou # 添加杭州的地理信息
geoadd china:city 108.948024 34.263161 xian # 添加西安的地理信息
```



> geopos 从`key`里返回所有给定位置元素的位置（经度和纬度）

```bash
geopos china:city xian # 获取西安的经度和纬度 108.94802302122116089 34.2631604414749944
```



> GEODIST key member1 member2 [unit]
>
> 返回两个给定位置之间的距离。
>
> 如果两个位置之间的其中一个不存在， 那么命令返回空值。

指定单位的参数 unit 必须是以下单位的其中一个：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

```bash
geodist china:city beijing shanghai km # 计算北京和上海之间的距离，单位为km，返回：1067.5980
```



> georadius 以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

范围可以使用以下其中一个单位：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

```ba
georadius china:city 110 30 1000 km # 获取 经度110 纬度30 这个点方圆1000km范围内的元素
georadius china:city 110 30 1000 km withdist # 结果带每个元素距离 经度110 纬度30 这个点的直线距离
georadius china:city 110 30 1000 km withcoord # 结果带每个元素的经纬度
georadius china:city 110 30 1000 km count 2 # 只显示两个查询结果 
```



> georadiusbymember key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]
>
> 这个命令和 [GEORADIUS](http://www.redis.cn/commands/georadius.html) 命令一样， 都可以找出位于指定范围内的元素， 但是 `GEORADIUSBYMEMBER` 的中心点是由给定的位置元素决定的， 而不是像 [GEORADIUS](http://www.redis.cn/commands/georadius.html) 那样， 使用输入的经度和纬度来决定中心点

```bash
# 找出指定元素周围的其他元素！
georadiusbymember china:city beijing 1000 km # 找出以北京为中心，方圆1000km内的元素，beijing xian
```



>geohash key member [member ...]

返回一个或多个位置元素的 [Geohash](https://en.wikipedia.org/wiki/Geohash) 表示。

该命令将返回11个字符的Geohash字符串。如果两个字符串越接近，那么这两个点距离越近！

```bash
geohash china:city beijing chongqing # 返回北京和重庆的Geohash字符串，wx4g0b7xrt0，wm78p86e170
```

 

> geo底层的实现原理是Zset，我们可以使用Zset命令来操作geo！

```bash
type china:city # 查看china:city的类型：zset
zrange china:city 0 -1 # 获取所有的城市，chongqing、xian、shenzhen、shanghai、beijing
zrem china:city beijing # 移除beijing这个元素
zrange china:city 0 -1 # chongqing、xian、shenzhen、shanghai
```



## Hyperloglog 

> 什么是基数？

基数：集合中不重复元素的个数

A：{1, 3, 5, 7, 8, 7}，基数为5

> 简介

Hyperloglog 是关于基数统计的算法！

优点：占用的内存是固定的，2^64不同元素的技术，只需要废12KB内存！如果要从内存角度来比较的话hyperloglog首选！

网页的UV（网站的浏览量），一个人访问一个网站多次，算作一次。

传统的方式，set保存用户的id，然后就可以统计set中元素的数量作为标准判断！

这个方式如果保存大量的用户id，就会比较麻烦！我们的目的是为了计数，而不是保存用户id；

0.81%错误率！统计UV任务，可以忽略不计！

> 测试使用

```bash
pfadd mykey a b c d e f g h i j i # 创建第一组元素
pfcount mykey # 统计mykey中元素的基数数量，10
pfadd mykey2 i j z x c v b n m # 创建第二组元素 mykey2
pfcount mykey2 9 # 统计mykey2元素的基数数量，9
pfmerge mykey3 mykey mykey2 # 合并两组 mykey mykey2 =》 mykey3 并集
pfcount mykey3 # 15
```

如果允许容错，那么一定可以使用Hyperloglog！

如果不允许容错，就使用set或者自己的数据类型即可！



## Bitmaps

> 位存储
>
> Bitmaps和hash结合，就有了布隆过滤器，用于解决缓存击穿问题

统计疫情感染人数：0 1 0 1

统计用户信息，活跃，不活跃，登录，未登录，打卡

两个状态的，都可以使用Bitmaps！

**Bitmaps位图**，数据结构，都是操作二进制来进行记录，就只有0和1两个状态！

> 例如：365天=365bit 1字节=8bit 46个字节左右

> 测试

使用Bitmaps记录周一到周日的打卡：

周一：1 周二：0 周三：1 周四：1 周五：1 周六：1...

```bash
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 1
(integer) 0
127.0.0.1:6379> setbit sign 6 1
(integer) 0
```

查看某一天是否有打卡

```bash
127.0.0.1:6379> getbit sign 3 # 查看周四是否打卡
(integer) 1
```

统计操作，统计打卡的天数！

```bash
127.0.0.1:6379> bitcount sign # 统计这周的打卡天数
(integer) 5
```



# 事务

> MySQL：ACID
>
> Redis单条命令是原子性的，但是事务不保证原子性！
>
> Redis事务没有隔离级别的概念
>
> 所有的命令在事务中，并没有直接被执行！只有发起执行命令的时候才会执行！Exec

Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行！

一次性、顺序性、排他性！执行一系列的命令！

```
--------队列 set set set 执行 ------- 
```

redis的事务：

* 开启事务（multi）
* 命令入队（...）
* 执行事务（exec）

锁：Redis可以实现乐观锁



> 执行事务

```bash
127.0.0.1:6379> multi # 开启事务
OK
# 命令入队
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec # 执行事务
1) OK
2) OK
3) "v2"
4) OK
```



> 放弃事务

```bash
127.0.0.1:6379> multi     # 开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> discard   # 取消事务
OK
127.0.0.1:6379> get key4  # 事务队列中的命令都不会被执行
(nil)
```



> 编译型异常（代码有问题！命令有错！），事务中所有的命令都不会被执行！

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> getset k3 # 错误的命令
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> set k5 v5
QUEUED
127.0.0.1:6379> exec # 执行事务报错！
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k4 # 所有的命令都不会被执行
(nil)
```



> 运行时异常（1/0），如果事务队列中存在逻辑错误的命令，那么执行事务的时候，该命令不会被执行（错误命令抛出异常），但是其他命令照常执行！

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> incr k1 # 存在逻辑错误，会执行失败
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec
1) OK
2) (error) ERR value is not an integer or out of range # 虽然 incr k1 这条命令报错了，但是其他命令依旧正常执行
3) OK
4) OK
5) "v3"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"v3"
127.0.0.1:6379> get k1
"v1"
```



# 监控

> watch：实现乐观锁

**悲观锁：**很悲观，认为什么时候都会出问题，无论做什么都会加锁！

**乐观锁：**很乐观，认为什么时候都不会出问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据，没有就修改，有就不修改（CAS算法）

* 获取version
* 更新的时候比较version

> Redis测监视测试

正常执行成功！

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money     # 监视money对象
OK
127.0.0.1:6379> multi           # 事务正常结束，数据期间没有发生变动，这个时候就正常执行成功！
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```



测试多线程修改值，使用watch可以进行redis的乐观锁操作！

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money     # 监视money对象
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10
QUEUED
127.0.0.1:6379> incrby out 10
QUEUED
127.0.0.1:6379> exec            # 执行之前，另外一个线程，修改了money的值，这个时候，事务就会执行失败，乐观锁，cas算法(compare and swap)
(nil)
```

如果修改失败，解锁，重新监视，获取最新值！

```bash
127.0.0.1:6379> unwatch          # 如果事务执行失败，就先解锁
OK
127.0.0.1:6379> watch money      # 重新监视money对象，获取最新的值，再次监视，相当于select version
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10
QUEUED
127.0.0.1:6379> incrby out 10
QUEUED
127.0.0.1:6379> exec             # 比对监视的值是否发生变化，如果没有发生变化，就执行成功，否则执行失败，执行失败就再执行上面两步（自旋锁）
1) (integer) 990
2) (integer) 10
```



# Jedis

> 使用Java操作Redis

## 什么是Jedis？

Jedis是官方推荐的Java连接开发工具！使用Java操作Redis的中间件！如果要使用Java操作Redis，一定要对Jedis十分熟悉！

> 测试

1、导入对应的依赖

```xml
<dependencies>
    <!--导入jedis的包-->
    <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.3.0</version>
    </dependency>
    <!--fastjson-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.73</version>
    </dependency>
</dependencies>
```

2、测试步骤

* 连接redis数据库

* 操作命令
* 断开连接



## 用Jedis连接阿里云等服务器上的redis

1. 配置redis.conf
   1.设置访问redis的密码：requirepass 要设置密码
   2.注释bind 127.0.0.1
   (重启redis-server服务,进入redis后要先验证密码,用这个命令：auth 密码 ,然后ping一下看有没有配置成功)
2. idea访问时添加auth密码
   Jedis jedis = new Jedis("服务器的外网ip",6379);
   jedis.auth("redis的密码");
   System.out.println(jedis.ping());
   （输出PONG的话就成功了）

```java
package com.yan;

import redis.clients.jedis.Jedis;

public class TestPing {
    public static void main(String[] args) {
        // 1、new Jedis对象即可
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456"); // redis登录密码，远程登录需要设置
        // jedis所有的命令就是我们之前学习的所有指令
        // 测试连接是否成功
        System.out.println(jedis.ping());
    }
}
```

![测试连接](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%B5%8B%E8%AF%95%E8%BF%9E%E6%8E%A5.png)



## 常用api

String

List

Set

Hash

Zset



> 对key的操作命令

```java
package com.yan;

import redis.clients.jedis.Jedis;

import java.util.Set;

public class TestPing {
    public static void main(String[] args) {
        // 1、new Jedis对象即可
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456"); // redis登录密码，远程登录需要设置
        // jedis所有的命令就是我们之前学习的所有指令
        // 测试连接是否成功
        System.out.println(jedis.ping());

        System.out.println("清空数据：" + jedis.flushDB());
        System.out.println("判断某个键是否存在：" + jedis.exists("username"));
        System.out.println("新增<'username', 'xiaoming'>的键值对：" + jedis.set("username", "xiaoming"));
        System.out.println("新增<'password', 'password'>的键值对：" + jedis.set("password", "password"));
        System.out.println("系统中的所有键如下：");
        Set<String> keys = jedis.keys("*");
        System.out.println(keys);
        System.out.println("删除键password：" + jedis.del("password"));
        System.out.println("判断键password是否存在：" + jedis.exists("password"));
        System.out.println("查看键username所存储的值的类型：" + jedis.type("username"));
        System.out.println("随机返回key空间中的一个：" + jedis.randomKey());
        System.out.println("重命名key：" + jedis.rename("username", "name"));
        System.out.println("取出改后的name：" + jedis.get("name"));
        System.out.println("按索引查询：" + jedis.select(0));
        System.out.println("删除当前选择数据中的所有key：" + jedis.flushDB());
        System.out.println("返回当前数据库中key的数目：" + jedis.dbSize());
        System.out.println("删除所有数据库中的所有key：" + jedis.flushAll());
    }
}
```



> 对String操作的命令

```java
package com.yan;

import redis.clients.jedis.Jedis;

import java.util.concurrent.TimeUnit;

public class TestString {
    public static void main(String[] args) {
        // 1、new Jedis对象即可
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456"); // redis登录密码，远程登录需要设置
        // jedis所有的命令就是我们之前学习的所有指令

        jedis.flushDB();
        System.out.println("============================增加数据============================");
        System.out.println(jedis.set("key1", "value1"));
        System.out.println(jedis.set("key2", "value2"));
        System.out.println(jedis.set("key3", "value3"));
        System.out.println("删除键key2：" + jedis.del("key2"));
        System.out.println("获取键key2：" + jedis.get("key2"));
        System.out.println("修改键key1" + jedis.set("key1", "value1Changed"));
        System.out.println("获取key1的值：" + jedis.get("key1"));
        System.out.println("在key3后面加入值：" + jedis.append("key3", "End"));
        System.out.println("key3的值：" + jedis.get("key3"));
        System.out.println("增加多个键值对：" + jedis.mset("key01", "value01", "key02", "value02", "key03", "value03"));
        System.out.println("获取多个键值对：" + jedis.mget("key01", "key02", "key03"));
        System.out.println("获取多个键值对：" + jedis.mget("key01", "key02", "key03", "key04"));
        System.out.println("删除多个键值对：" + jedis.del("key01", "key02"));
        System.out.println("获取多个键值对：" + jedis.mget("key01", "key02", "key03"));

        jedis.flushDB();
        System.out.println("============================新增键值对防止覆盖原先值============================");
        System.out.println(jedis.setnx("key1", "value1"));
        System.out.println(jedis.setnx("key2", "value2"));
        System.out.println(jedis.setnx("key2", "value2-new"));
        System.out.println(jedis.get("key1"));
        System.out.println(jedis.get("key2"));

        System.out.println("============================新增键值对并设置有效时间============================");
        System.out.println(jedis.setex("key3", 2, "value3"));
        System.out.println(jedis.get("key3"));
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(jedis.get("key3"));

        System.out.println("============================获取原值，更新为新值============================");
        System.out.println(jedis.getSet("key2", "key2GetSet"));
        System.out.println(jedis.get("key2"));

        System.out.println("获取key2的值的子串：" + jedis.getrange("key2", 2, 4));
    }
}
```



> 对List操作的命令

```java
package com.yan;

import redis.clients.jedis.Jedis;

public class TestList {
    public static void main(String[] args) {
        // 1、new Jedis对象即可
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456"); // redis登录密码，远程登录需要设置
        // jedis所有的命令就是我们之前学习的所有指令

        jedis.flushDB();
        System.out.println("============================添加一个list============================");
        jedis.lpush("collections", "ArrayList", "Vector", "Stack", "HashMap", "WeakHashMap", "LinkedHashMap");
        jedis.lpush("collections", "HashSet");
        jedis.lpush("collections", "TreeSet");
        jedis.lpush("collections", "TreeMap");
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1)); // -1代表倒数第一个元素，-2代表倒数第二个元素
        System.out.println("collections区间0-3的元素：" + jedis.lrange("collections", 0, 3));
        System.out.println("========================================================");
        System.out.println("删除指定个数的元素：" + jedis.lrem("colletions", 2, "HashMap"));
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1));
        System.out.println("截取[0,3]区间的元素：" + jedis.ltrim("collections", 0, 3));
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1));
        System.out.println("collections列表出栈（左端）：" + jedis.lpop("collections"));
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1));
        System.out.println("collections添加元素，从列表右端，与lpush对应：" + jedis.rpush("collections", "EnumMap"));
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1));
        System.out.println("collections列表出栈（右端）：" + jedis.rpop("collections"));
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1));
        System.out.println("修改collections指定下标1的内容：" + jedis.lset("collections", 1, "LinkedArrayList"));
        System.out.println("collections的内容：" + jedis.lrange("collections", 0, -1));
        System.out.println("========================================================");
        System.out.println("collections的长度：" + jedis.llen("collections"));
        System.out.println("获取collections下标为2的元素：" + jedis.lindex("collections", 2));
        System.out.println("========================================================");
        jedis.lpush("sortedList", "3", "6", "2", "0", "7", "4");
        System.out.println("sortedList排序前：" + jedis.lrange("sortedList", 0, -1));
        System.out.println(jedis.sort("sortedList"));
        System.out.println("sortedList排序后：" + jedis.lrange("sortedList", 0, -1));

    }
}
```



> 对Set的操作命令

```java
package com.yan;

import redis.clients.jedis.Jedis;

public class TestSet {
    public static void main(String[] args) {
        // 1、new Jedis对象即可
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456"); // redis登录密码，远程登录需要设置
        // jedis所有的命令就是我们之前学习的所有指令

        jedis.flushDB();
        System.out.println("============================向集合中添加元素（不重复）============================");
        System.out.println(jedis.sadd("eleSet", "e1", "e2", "e4", "e3", "e0", "e8", "e7", "e5"));
        System.out.println(jedis.sadd("eleSet", "e6"));
        System.out.println(jedis.sadd("eleSet", "e6"));
        System.out.println("eleSet的所有元素为：" + jedis.smembers("eleSet"));
        System.out.println("删除一个元素e0：" + jedis.srem("eleSet", "e0"));
        System.out.println("eleSet的所有元素为：" + jedis.smembers("eleSet"));
        System.out.println("删除两个元素e7和e6：" + jedis.srem("eleSet", "e7", "e6"));
        System.out.println("eleSet的所有元素为：" + jedis.smembers("eleSet"));
        System.out.println("随机的移除集合中的一个元素：" + jedis.spop("eleSet"));
        System.out.println("随机的移除集合中的一个元素：" + jedis.spop("eleSet"));
        System.out.println("eleSet的所有元素为：" + jedis.smembers("eleSet"));
        System.out.println("eleSet中包含元素的个数：" + jedis.scard("eleSet"));
        System.out.println("e3是否在eleSet中：" + jedis.sismember("eleSet", "e3"));
        System.out.println("e1是否在eleSet中：" + jedis.sismember("eleSet", "e1"));
        System.out.println("========================================================");
        System.out.println(jedis.sadd("eleSet1", "e1", "e2", "e4", "e3", "e0", "e8", "e7", "e5"));
        System.out.println(jedis.sadd("eleSet2", "e1", "e2", "e4", "e3", "e0", "e8"));
        System.out.println("将eleSet1中的e1移动到eleSet3中：" + jedis.smove("eleSet1", "eleSet3", "e1"));
        System.out.println("将eleSet1中的e2移动到eleSet3中：" + jedis.smove("eleSet1", "eleSet3", "e2"));
        System.out.println("eleSet1中的元素：" + jedis.smembers("eleSet1"));
        System.out.println("eleSet3中的元素：" + jedis.smembers("eleSet3"));
        System.out.println("============================集合运算============================");
        System.out.println("eleSet1中的元素：" + jedis.smembers("eleSet1"));
        System.out.println("eleSet2中的元素：" + jedis.smembers("eleSet2"));
        System.out.println("eleSet1和eleSet2的交集：" + jedis.sinter("eleSet1", "eleSet2"));
        System.out.println("eleSet1和eleSet2的并集：" + jedis.sunion("eleSet1", "eleSet2"));
        System.out.println("eleSet1和eleSet2的差集：" + jedis.sdiff("eleSet1", "eleSet2")); // eleSet1排除eleSet2的那部分
        jedis.sinterstore("eleSet4", "eleSet1", "eleSet2"); // 求交集并将交集保存到eleSet4集合中
        System.out.println("eleSet4中的元素：" + jedis.smembers("eleSet4"));
    }
}
```



> 对Hash的操作命令

```java
package com.yan;

import redis.clients.jedis.Jedis;

import java.util.HashMap;
import java.util.Map;

public class TestHash {
    public static void main(String[] args) {
        // 1、new Jedis对象即可
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456"); // redis登录密码，远程登录需要设置
        // jedis所有的命令就是我们之前学习的所有指令

        jedis.flushDB();
        Map<String, String> map = new HashMap<String, String>();
        map.put("key1", "value1");
        map.put("key2", "value2");
        map.put("key3", "value3");
        map.put("key4", "value4");
        // 添加名称为hash(key)的hash元素
        jedis.hmset("hash", map);
        // 向名称为hash的hash中添加key为key5, value为value5的元素
        jedis.hset("hash", "key5", "value5");
        System.out.println("散列hash的所有键值对为：" + jedis.hgetAll("hash")); // return Map<String, String>
        System.out.println("散列hash的所有键为：" + jedis.hkeys("hash")); // return Set<String>
        System.out.println("散列hash的所有值为：" + jedis.hvals("hash")); // // return List<String>
        System.out.println("将key6保存的值加上一个整数，如果key6不存在，则添加key6：" + jedis.hincrBy("hash", "key", 6));
        System.out.println("散列hash的所有键值对为：" + jedis.hgetAll("hash"));
        System.out.println("将key6保存的值加上一个整数，如果key6不存在，则添加key6：" + jedis.hincrBy("hash", "key", 3));
        System.out.println("散列hash的所有键值对为：" + jedis.hgetAll("hash"));
        System.out.println("删除一个或者多个键值对：" + jedis.hdel("hash", "key2"));
        System.out.println("散列hash的所有键值对为：" + jedis.hgetAll("hash"));
        System.out.println("散列hash的所有键值对的个数：" + jedis.hlen("hash"));
        System.out.println("判断hash中是否存在key2：" + jedis.hexists("hash", "key2"));
        System.out.println("判断hash中是否存在key3：" + jedis.hexists("hash", "key3"));
        System.out.println("获取hash中的值：" + jedis.hmget("hash", "key3"));
        System.out.println("获取hash中的值：" + jedis.hmget("hash", "key3", "key4"));
    }
}
```



> 事务操作

```java
package com.yan;

import com.alibaba.fastjson.JSONObject;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;

public class TestTX {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("远程服务器ip地址", 端口号);
        jedis.auth("123456");

        jedis.flushDB();

        JSONObject jsonObject = new JSONObject();
        jsonObject.put("hello", "world");
        jsonObject.put("name", "xiaoqiang");

        // 开启事务
        Transaction multi = jedis.multi();
        String result = jsonObject.toJSONString();
//        String watch = jedis.watch(result); // 监控result对象，实现乐观锁

        try {
            multi.set("user1", result);
            multi.set("user2", result);
            int i = 1/0;  // 代码抛出异常，事务执行失败
            multi.exec(); // 执行事务！
        } catch (Exception e) {
            multi.discard(); // 放弃事务
            e.printStackTrace();
        } finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            jedis.close(); // 关闭连接，无论是否出现异常都会运行finally中的代码
        }


    }
}
```



# SpringBoot整合

SpringBoot操作数据：spring-data jpa jdbc mongodb redis！

SpringData也是和SpringBoot齐名的项目！

说明：在SpringBoot2.x之后，原来使用的jedis被替换为lettuce

jedis：采用直连，多个线程操作的话，是不安全的，如果想要避免不安全，使用jedis pool连接池！更像BIO模式

lettuce: 采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况！可以减少线程数量，更像NIO模式

源码分析：

```java
/*
 * Copyright 2012-2019 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot.autoconfigure.data.redis;

import java.net.UnknownHostException;

import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;

/**
 * {@link EnableAutoConfiguration Auto-configuration} for Spring Data's Redis support.
 *
 * @author Dave Syer
 * @author Andy Wilkinson
 * @author Christian Dupuis
 * @author Christoph Strobl
 * @author Phillip Webb
 * @author Eddú Meléndez
 * @author Stephane Nicoll
 * @author Marco Aust
 * @author Mark Paluch
 * @since 1.0.0
 */
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate") // 我们可以自己定义一个redisTemplate来替换这个默认的
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
        // 默认的RedisTemp没有过多的设置，redis对象都是需要序列化的！
        // 两个泛型都是Object类型，我们后面使用需要强制转换<String, Object>
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

	@Bean
	@ConditionalOnMissingBean // 由于String是redis中最常使用的类型，所以单独提出来了一个bean
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

}
```



> 整合测试

1、导入依赖

```xml
<dependencies>
    <!--操作redis-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```



2、配置连接

```properties
# SpringBoot 所有的配置类，都有一个自动配置类 RedisAutoConfiguration
# 自动配置类都会绑定一个properties配置文件   RedisProperties

# 配置redis
spring.redis.host=服务器ip地址
spring.redis.port=端口号
spring.redis.password=redis登录密码
```



3、测试

```java
package com.yan;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.core.RedisTemplate;

@SpringBootTest
class Redis02SpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {

        // redisTemplate 操作不同的数据类型，api和我们的指令是一样的
        // opsForValue 操作字符串 类似String
        // opsForList 操作list 类似List
        // opsForSet
        // opsForHash
        // opsForZset
        // opsForGeo
        // opsForHyperLogLog

        //除了基本的操作，常用的方法都可以直接使用redisTemplate操作，比如事务和基本的crud
        // 获取redis的连接对象
//        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
//        connection.flushDb();

        redisTemplate.opsForValue().set("mykey1", "狂神说Java");
        redisTemplate.opsForValue().set("mykey2", "kuangshen");
        System.out.println(redisTemplate.opsForValue().get("mykey1"));
        System.out.println(redisTemplate.opsForValue().get("mykey2"));
    }

}
```



redisTemplate默认的序列化配置：

![redisTemplate序列化配置](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redisTemplate%E5%BA%8F%E5%88%97%E5%8C%96%E9%85%8D%E7%BD%AE.png)

![JDK序列化](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/JDK%E5%BA%8F%E5%88%97%E5%8C%96.png)



对象的保存需序列化：

![对象的保存需要序列化](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%AF%B9%E8%B1%A1%E7%9A%84%E4%BF%9D%E5%AD%98%E9%9C%80%E8%A6%81%E5%BA%8F%E5%88%97%E5%8C%96.png)



自定义RedisTemplate：

```java
package com.yan.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.net.UnknownHostException;

@Configuration
public class RedisConfig {

    // 编写我们自己的redisTemplate
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        // 我们为了自己开发方便，一般直接使用<String, Object>
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);

        // 配置具体的序列化方式
        // Json序列化配置
        Jackson2JsonRedisSerializer<Object> jsonRedisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jsonRedisSerializer.setObjectMapper(om);
        // String的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value的序列化方式采用jackson
        template.setValueSerializer(jsonRedisSerializer);
        // hash的value序列化方式采用jackson
        template.setHashValueSerializer(jsonRedisSerializer);
        template.afterPropertiesSet();

        return template;
    }

}
```



# Redis.conf详解

启动的时候，就通过配置文件启动的。

> 单位

![redis的单位](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E7%9A%84%E5%8D%95%E4%BD%8D.png)

1、配置文件 unit单位 对大小写不敏感！

> 包含

![组合多个conf文件](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%BB%84%E5%90%88%E5%A4%9A%E4%B8%AAconf%E6%96%87%E4%BB%B6.png)

包含多个配置文件

> 网络

```bash
bind 127.0.0.1 # 绑定的ip
protected-mode yes # 保护模式
port 6379 # 端口设置
```

> 通用 GENERAL

```bash
daemonize yes # 以守护进程的方式运行，默认是no，我们需要自己开启为yes
pidfile /var/run/redis_6379.pid # 如果以守护进程的方式运行，我们就需要指定一个pid文件

# 日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably) 生产环境
# warning (only very important / critical messages are logged)
loglevel notice

logfile ""   # 日志文件的保存位置
databases 16 # 数据库的数量，默认是16个数据库
always-show-logo yes # 是否显示logo
```

> 快照 SNAPSHOTTING

持久化，在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb .aof

redis是内存数据库，如果没有持久化，那么数据断电即失！

```bash
# 900s内，有key进行了修改，则进行持久化操作
save 900 1
# 300s内，至少有10个key进行了修改，则进行持久化操作
save 300 10
# 60s内，至少有10000个key进行了修改，则进行持久化操作
save 60 10000
# 我们之后学习持久化，会自己设置上面的值

stop-writes-on-bgsave-error yes # 持久化如果出错，是否还需要继续工作

rdbcompression yes # 是否压缩rdb文件，需要消耗一些cpu资源

rdbchecksum yes # 保存rdb文件的时候，计算校验和，校验文件完整性

dir ./  # rdb文件保存的目录
```



> 复制 REPLICATION，后面学习主从复制时，再进行学习



> 安全 SECURITY

获取密码：

```bash
# redis-cli
config get requirepass
```

设置redis的密码，默认是没有密码的！

```bash
config set requirepass 123456 # 设置redis的密码
auth 123456 # 使用密码进行登录
```



> 限制 CLIENTS

```bash
maxclients 10000  # 设置能连上redis-server的最大客户端数量
maxmemory <bytes> # redis 配置最大的内存容量
maxmemory-policy noeviction # 内存达到上限后的处理策略

maxmemory-policy 六种方式
1、volatile-lru：只对设置了过期时间的key进行LRU（默认值） 
2、allkeys-lru ： 删除lru算法的key   
3、volatile-random：随机删除即将过期key   
4、allkeys-random：随机删除   
5、volatile-ttl ： 删除即将过期的   
6、noeviction ： 永不过期，返回错误
```



> APPEND ONLY MODE模式 aof配置

```bash
appendonly no # 默认是不开启aof模式的，默认使用rdb方式持久化，在大部分情况下，rdb完全够用！
appendfilename "appendonly.aof" # 持久化文件的名字

# appendfsync always  # 每次修改都会sync，消耗性能
appendfsync everysec  # 每秒执行一次 sync，可能会丢失这1s的数据
# appendfsync no      # 不执行sync，这个时候操作系统自己实现同步数据，速度最快！
```



# Redis持久化

面试和工作，持久化都是重点！

Redis内存数据库，如果不将内存中的数据保存到磁盘，服务器进程退出，服务器中的数据库状态也会消失。所以Redis提供了持久化功能！



## RDB（Redis DataBase）

> 什么是RDB

![rdb](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/rdb.png)

在指定时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，数据库恢复时，直接将快照文件读入内存中。

Redis会单独创建（fork）一个子进程来持久化，会将数据写入到一个临时文件中，待持久化过程结束，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程不进行任何IO操作。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加高效。RDB的缺点是最后一次持久化后的数据可能丢失。

默认是采用RDB持久化，一般情况下不需要修改这个配置！

在生产环境一般会对dump.rdb文件进行备份

**rdb保存的文件是 dump.rdb**

> 在配置文件的快照（SNAPSHOTTING）中进行配置

```bash
# The filename where to dump the DB
dbfilename dump.rdb # rdb保存的文件名
```

```bash
# save 900 1
# save 300 10
# save 60 10000
save 60 5 # 测试：只要60s内修改了5次key，就会触发rdb操作
```

1、save的规则满足的情况下，会自动生成dump.rdb文件

2、执行flushall命令，也会自动生成dump.rdb文件

3、退出redis，也会生成dump.rdb文件！

![rdb文件](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/rdb%E6%96%87%E4%BB%B6.png)

> 如何恢复rdb文件

1、只需要将rdb文件放在redis启动目录就行了，redis启动的时候会自动检查dump.rdb，并恢复其中的数据！

2、查看配置文件目录：

![查看配置文件目录](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%9F%A5%E7%9C%8B%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95.png)

```bash
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/bin/myredisconifg" # 如果在这个目录下存在dump.rdb文件，启动就会自动恢复其中的数据
```

**优点：**

1、适合大规模的数据恢复！dump.rdb

2、对数据完整性要求不高

**缺点：**

1、需要一定的时间间隔进行操作！如果redis意外宕机了，最后一次修改的数据就没了！

2、fork进程的时候会占用一定的内存空间！



# AOF（Append Only File）

将我们的所有命令都记录下来，history，恢复的时候就把这个文件中的命令全部执行一遍！

> 是什么

![aof](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/aof.png)

以日志的新式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录）只许追加文件但不能改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话，就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

**AOF保存的是appendonly.aof文件**

配置文件中配置AOF的部分

>  APPEND ONLY MODE

AOF默认是不开启的，我们需要手动开启

```bash
appendonly yes
```

重启服务，AOF配置就生效了

**如果appendonly.aof文件有错位，redis服务是启动不了的，我们需要修复这个aof文件**

redis给我们提供了一个工具：`redis-check-aof `

```bash
redis-check-aof --fix appendonly.aof
```

`redis-check-aof`会删除错误的命令

```bash
appendonly no # 默认是不开启aof模式的，默认使用rdb方式持久化，在大部分情况下，rdb完全够用！
appendfilename "appendonly.aof" # 持久化文件的名字

# appendfsync always  # 每次修改都会sync，消耗性能
appendfsync everysec  # 每秒执行一次 sync，可能会丢失这1s的数据
# appendfsync no      # 不执行sync，这个时候操作系统自己实现同步数据，速度最快！

# rewrite 重写
```

aof默认就是文件的无限追加，文件会越来越大！

如果aof文件大于64mb，会fork一个新进程来将我们的文件进行重写！

**优点：** 

1、每次修改都同步，文件的完整性会更好！

2、每秒同步一次，可能会丢失一秒的数据

3、不同步，效率最高！

**缺点：**

1、相对数据文件来说，aof远远大于rdb，修复速度也比rdb慢！

2、aof运行效率也比rdb慢，redis默认配置是rdb持久化！



# Redis发布订阅

Redis发布订阅（pub/sub）是一种**消息通信模式**：发送者（pub）发送消息，订阅者（sub）接收消息。微信、微博、关注系统！

Redis客户端可以订阅任意数量的频道。

订阅/发布消息图：

三个角色：

* 消息发送者
* 频道
* 消息订阅者

![redis发布订阅](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E5%8F%91%E5%B8%83%E8%AE%A2%E9%98%85.png)

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![pubsub1](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/pubsub1.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![pubsub2](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/pubsub2.png)

> 命令

这些命令被广泛用于构建即时通信应用，比如网络聊天室（charroom）和实时广播、实时提醒等。

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [PSUBSCRIBE pattern [pattern ...\]](https://www.runoob.com/redis/pub-sub-psubscribe.html) 订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...\]]](https://www.runoob.com/redis/pub-sub-pubsub.html) 查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html) 将信息发送到指定的频道。 |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html) 退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]](https://www.runoob.com/redis/pub-sub-subscribe.html) 订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...\]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html) 指退订给定的频道。 |

> 测试

订阅者：

```bash
127.0.0.1:6379> SUBSCRIBE mychannel           # 订阅mychannel频道
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "mychannel"
3) (integer) 1
# 等待读取推送的信息
1) "message"        # 信息
2) "mychannel"		# 频道名
3) "hello world!"	# 信息具体内容
```

发布者：

```bash
127.0.0.1:6379> PUBLISH mychannel "hello world!" # 发布者向mychannel频道发送"hello world!"消息
(integer) 1
```



>原理

Redis是使用C实现的，通过分析Redis源码里的pubsub.c文件，了解发布和订阅机制的底层实现，借此加深对Redis的理解。

Redis通过publish、subscribe和psubscribe等命令实现发布和订阅功能。

通过subscribe命令订阅某频道后，redis-server里维护一个字典，字典的键就是一个个的channel，而字典的值则是一个链表，链表中保存了所有订阅这个channel的客户端。subscribe命令的关键，就是将客户端添加到给定的channel的订阅链表中。

通过publish命令向订阅者发送消息，redis-server会使用给定的频道作为键，在它所维护的channel字典中查找记录了订阅这个频道的所有客户端的链表，遍历这个链表，将消息发布给所有的订阅者。

Pub/Sub从字面上理解就是发布（Publish）与订阅（Subscribe），在Redis中，可以设定对某个key值进行消息发布及消息订阅，当一个key值上进行了消息发布后，所有订阅它的客户端都会收到相应的消息。这一功能最明显的用法就是用作实时消息系统，比如普通的即时聊天，群聊等功能。

**使用场景：**

1、实时消息系统！

2、实时聊天！（频道当作聊天室，将消息回显给所有人即可）

3、订阅，关注系统都是可以的！

稍微复杂的场景会使用消息中间件：MQ（消息队列）



# Redis主从复制

## 概念

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器，前者称为主节点（master/leader），后者称为从节点（slave/follower），**数据的复制是单向的，只能从主节点到从节点**。Master以写为主，Slave以读为主。

**默认情况下，每台Redis服务器都是主节点；**且一个主节点可以有多个从节点（或没有从节点），但一个从节点只能有一个主节点。

主从复制的作用主要包括：

1、数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。

2、故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。

3、负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。

4、高可用基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制时Redis高可用的基础。



一般来说，要将Redis应用到工程项目中，只使用一台Redis是万万不能的，原因入下：

1、从结构上，单个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大；

2、从容量上，单个Redis服务器内存容量有限，就算一台Redis服务器内存容量由256GB，也不能将所有内存用作Redis存储内存，一般来说，**单台Redis最大使用内存不应该超过20GB**。

电商网站上的商品，一般都是一次上传，无数次浏览的，说专业点也就是“读多少写”。

对于这种场景，我们可以使用如下这种架构：

![redis集群架构](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E9%9B%86%E7%BE%A4%E6%9E%B6%E6%9E%84.png)



## 环境配置

### 只配置从结点，不用配置主结点！

```bash
127.0.0.1:6379> info replication # 查看当前结点的信息
# Replication
role:master                      # 角色 master
connected_slaves:0				 # 没有从结点
master_replid:4b845ff597476841e7e032fa47e7851d6adb63ab
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```



### 复制三个配置文件，修改对应的信息

```bash
cp redis.conf redis79.conf
cp redis.conf redis80.conf
cp redis.conf redis81.conf
```



* 端口

```bash
port 6381
```

* pidfile 守护进程名

```bash
pidfile /var/run/redis_6381.pid
```

* logfile log日志名

```bash
logfile "6381.log"
```

* dbfilename rdb文件名

```bash
dbfilename dump6381.rdb
```

* masterauth 配置主节点密码

```bash
masterauth 123456
```



### 开三个终端启动对应的redis服务

```bash
redis-server redis79.conf
redis-server redis80.conf
redis-server redis81.conf
```



### 查看redis进程

```bash
ps -ef | grep redis
```

![redis进程](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/redis%E8%BF%9B%E7%A8%8B.png)



## 一主二从

**默认情况下，每个结点都是主结点**，一般情况下只用配置从机就好了。

一主（6379）二从（6380、6381）

```bash
redis-cli -p 6379 # 开启对应的客户端
redis-cli -p 6380
redis-cli -p 6381
```



在6380和6381两个从结点中进行配置，在客户端中输入如下命令：

```bash
slaveof 127.0.0.1 6379 # slaveof 主结点IP地址 主结点端口号
```

如果搭建成功，主节点上可以看到两个从节点信息：

![集群搭建成功](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9B%86%E7%BE%A4%E6%90%AD%E5%BB%BA%E6%88%90%E5%8A%9F.png)

真实的主从配置应该在配置文件中配置，这样话是永久配置，我们这里使用的是命令，是暂时的配置（重启服务后，会变成主机），配置从节点的配置文件，配置REPLICATION部分。

```bash
# REPLICATION部分
replicaof <masterip> <masterport> # 主节点IP 主节点端口号
masterauth <master-password> # 主节点密码
```



> 细节

主结点负责写（也可以读），从结点只能读！主结点中的所有信息，都会被从结点保存。

主结点写：

```bash
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> get k1
"v1"
```

从结点只能读：

```bash
127.0.0.1:6381> set k1 v1
(error) READONLY You can't write against a read only replica.
127.0.0.1:6381> get k1
"v1"
127.0.0.1:6381> keys *
1) "k1"
```



> 测试：主节点断开连接，从结点依旧连接到主节点的，但是没有写操作了，这时，主机如果回复连接，从机依旧可以直接获取到主机写的内容！

如果使用命令行配置主从，服务重启后，该结点会变回主机（默认配置），只要变为从机，该节点就能获取到主机中的数据。

> 复制原理

Slave启动成功连接到Master后会发送一个sync同步命令

Master接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕后，**Master将传送整个数据文件到Slave，并完成一次完全同步**。

**全量复制**：Slave服务在接收到数据库文件数据后（Slave连接Master后），将其存盘并加载到内存中。

**增量复制**：Master继续将新的所有收集到的修改命令（连接后主机新增的数据）依次传给Slave，完成同步。

但是只要重新连接Master，一次完全同步（全量复制）将被自动执行。



> 层层链路

上一个Master连接下一个Slave

![层层链路](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%B1%82%E5%B1%82%E9%93%BE%E8%B7%AF.png)

这种模式也可以完成主从复制！

 

> 如果没有老大了，这个时候需要手动选择一个老大！

如果主机断开连接，我们可以使用`slaveof no one`让自己变成主节点！其他的节点就可以手动连接到最新的这个主节点（手动）！如果这个时候老大恢复了，这个谋权篡位的小弟也不会变成老大小弟。



# 哨兵模式

（自动选举老大的模式）

> 概述

主从切换技术的方法是：当主服务器宕机后，需要手动把一台服务器切换为主服务器，这就需要人工干预，费时费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，我们优先考虑哨兵模式。Redis从2.8开始正式提供了Sentinel（哨兵）架构来解决该问题。

谋权篡位的自动版，后台能够监控主机是否故障，如果故障了根据投票数**自动将从库转换为主库**。

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，他会独立运行，其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行多个Redis实例。**

![哨兵模式](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%93%A8%E5%85%B5%E6%A8%A1%E5%BC%8F.png)

这里的哨兵有两个作用

* 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。

* 当哨兵检测到master宕机，会自动将Slave切换成Master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控。这样就形成了多哨兵模式。

![多哨兵模式](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E5%93%A8%E5%85%B5%E6%A8%A1%E5%BC%8F.png)

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover（重新选举）过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象称为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover[故障转移]操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。



> 测试

我们目前的状态是一主二从！

1、配置哨兵配置文件：sentinel.conf

```bash
# sentinel monitor 被监控的名称 主机IP地址 主机端口号 有多少个哨兵认为master挂了，master才算真的挂了
sentinel monitor myredis 127.0.0.1 6379 1
```

2、启动哨兵！

```bash
root@be31dcbb77f1:/usr/local/bin/myredisconifg# redis-sentinel sentinel.conf
1861:X 31 Aug 2020 15:41:11.175 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1861:X 31 Aug 2020 15:41:11.175 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=1861, just started
1861:X 31 Aug 2020 15:41:11.175 # Configuration loaded
1861:X 31 Aug 2020 15:41:11.177 * Increased maximum number of open files to 10032 (it was originally set to 1024).
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 6.0.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 1861
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

1861:X 31 Aug 2020 15:41:11.178 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1861:X 31 Aug 2020 15:41:11.194 # Sentinel ID is cb25777acee82df55ddae3f8cae1e39ee9e70d21
1861:X 31 Aug 2020 15:41:11.195 # +monitor master myredis 127.0.0.1 6379 quorum 1
1861:X 31 Aug 2020 15:41:41.233 # +sdown master myredis 127.0.0.1 6379
1861:X 31 Aug 2020 15:41:41.233 # +odown master myredis 127.0.0.1 6379 #quorum 1/1
1861:X 31 Aug 2020 15:41:41.233 # +new-epoch 1
1861:X 31 Aug 2020 15:41:41.233 # +try-failover master myredis 127.0.0.1 6379
1861:X 31 Aug 2020 15:41:41.236 # +vote-for-leader cb25777acee82df55ddae3f8cae1e39ee9e70d21 1
1861:X 31 Aug 2020 15:41:41.236 # +elected-leader master myredis 127.0.0.1 6379
1861:X 31 Aug 2020 15:41:41.236 # +failover-state-select-slave master myredis 127.0.0.1 6379
1861:X 31 Aug 2020 15:41:41.291 # -failover-abort-no-good-slave master myredis 127.0.0.1 6379
1861:X 31 Aug 2020 15:41:41.374 # Next failover delay: I will not start a failover before Mon Aug 31 15:47:41 2020
```

如果Master节点断开了，这个时候就会在从机中随机选择一个服务器（这里有一个投票算法！）

![哨兵选的新主节点](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%93%A8%E5%85%B5%E9%80%89%E7%9A%84%E6%96%B0%E4%B8%BB%E8%8A%82%E7%82%B9.png)

哨兵日志：

![哨兵日志](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%93%A8%E5%85%B5%E6%97%A5%E5%BF%97.png)

如果主机此时回来了，只能归并到新的主机下，当作从机，这就是哨兵模式的规则！

> 哨兵模式

优点：

1、哨兵集群，基于主从复制模式，所有的主从配置优点，它全有

2、主从可以切换，故障可以转移，系统的可用性就会更好

3、哨兵模式就是主从模式的升级，手动到自动，更加健壮！

缺点：

1、Redis不好在线扩容，集群容量一旦到达上限，在线扩容会十分麻烦！

2、实现哨兵模式的配置其实是很麻烦的，里面有很多选择！

> 哨兵模式的全部配置！

```bash
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379；如果有哨兵集群，我们还需要配置每个哨兵的端口
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 2
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
这个数字越小，完成failover所需的时间就越长，
但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 
 
 
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
一个是事件的类型，
一个是事件的描述。
如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
 sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
```



# Redis缓存穿透和雪崩

Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。同时，他也带来了一些问题。其中，最要害的问题，就是数据的一致性问题，从严格意义上讲，这个问题无解。如果对数据的一致性要求很高，那么就不能使用缓存。

另外的一些典型问题就是，缓存穿透、缓存雪崩和缓存击穿。目前，业界也都比较流行的解决方案。

![数据库查询](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9F%A5%E8%AF%A2.png)



## 缓存穿透（查不到）

> 概念

缓存穿透的概念很简单，用户想要查询一个数据，发现Redis内存数据库中没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中（秒杀！），于是都去请求持久层数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。

> 解决方案

**布隆过滤器**

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免了对底层存储系统的查询压力；

![布隆过滤器](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8.png)

**缓存空对象**

当存储层不命中后，即时返回空对象也将其缓存起来，同时设置一个过期时间，之后再访问这个数据将会从缓存中获取，保护了后端数据源；

![缓存空对象](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%BC%93%E5%AD%98%E7%A9%BA%E5%AF%B9%E8%B1%A1.png)

但是这种方法会存在两个问题：

1、如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键；

2、即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务也有影响。



## 缓存击穿（查询量太大，缓存过期！）

> 概述

这里需要注意和缓存击穿的区别，缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。

当某个key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致数据库瞬间压力过大。

> 解决方案

**设置热点数据永不过期**

从缓存层面来看，没有设置过期时间，所以不会出现热点key过期后产生的问题。

**加互斥锁**

分布式锁：使用分布式锁，保证对于每个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可。这个方式将高并发的压力转移到了分布式锁，因此对分布式锁的考验很大。



## 缓存雪崩

> 概念

缓存雪崩，是指在某个时间段，缓存集中过期失效。Redis宕机！

产生雪崩的原因之一，比如在写文本的时候，马上就要到双十二零点，很快就会引来一波抢购，这波商品时间比较集中的放入了缓存，假设缓存一个小时。那么到了凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。于是所有的请求都会到达存储层，存储层的调用量会爆增，造成存储层也会挂掉的情况。

![缓存雪崩](/images/Redis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%BC%93%E5%AD%98%E9%9B%AA%E5%B4%A9.png)

其实集中过期，倒不是非常致命，比较致命的缓存雪崩，是缓存服务器某个节点宕机或断网。因为自然形成的缓存雪崩，一定是在某个时间段集中创建缓存，这个时候，数据库也是可以顶住压力的。无非就是对数据库产生周期性的压力而已。而缓存服务器节点的宕机，对数据库服务器造成的压力是不可预知的，很可能瞬间就把数据库压垮。

> 解决方案

**Redis高可用**

这个思想的含义是，既然Redis有可能挂掉，那我多增设几台Redis，这样一台挂掉了，其他的还可以继续工作，其实就是搭建集群。

**限流降级**（在SpringCloud中有涉及）

这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

**数据预热**

数据加热的含义就是在正式部署之前，先把可能的数据预先访问一遍，这部分可能大量访问的数据就会加载在缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。