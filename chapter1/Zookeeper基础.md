# Zookeeper基础

## 综述

zookeeper是一个开源的分布式协同服务系统,封装了一些分布式协同服务,提供了简单的api供用户使用

zookeeper是高可用的

起源于雅虎的协同服务系统

zookeeper被广泛使用:

hadoop:用于做NameNode高可用

Hbase

kafka: 做组成员管理,leader选举

应用场景:

配置管理

DNS服务

组成员管理

分布式锁

## zookeeper提供什么服务

数据模型: 类似于文件系统tree(区别与文件系统,每个节点可以存取数据,每个节点都有版本号(从0开始))

dataTree接口:

使用路径名定位zNode(一个路径就是一个zNode)

zNode只支持全量写入和读取

dataTree所有API都是wait-free的

dataTree不提供锁子类的分布式协同机制,但可以使用基础API灵活实现多种分布式协同机制

## zNode分类

持久性zNode(PERSISTENT)

临时性zNode(EPHEMERAL)

有序的/无序的

## zookeeper安装及zkCli的使用

下载zookeeper

配置配置文件(默认使用zoo.cfg)

1. 客户端连接使用的端口

2. dataDir: 日志文件/数据文件的目录

配置环境变量

启动zookeeper服务: zkServer.sh start

客户端连接zookeeper: zkCli.sh

进入zkCli后可以执行zookeeper客户端命令如: create/delete等

可以通过创建临时节点的方式实现分布式锁

## 通过zookeeper实现master-worker协同

Hbase/kafka/hdfs使用的这种模式

如何使用zookeeper实现:

1. mster行使master职能前先创建临时节点/master,创建成功进入master角色,
active-master失败,他创建的节点/master节点就会被删除,
backup-master监控/master节点,此时会收到通知,再次创建/master节点成为active-master

2. worker通过在/workers下创建临时节点加入集群

3. active-master会监控/workers下的znode的变化,workers变化时,实时获取workers下的znode获取workes列表

使用`create -e master`创建master;`stat -w /master`监控master节点;使用`ls -w /workers`监控workers

## zookeeper架构解析

应用使用zookeeper客户端使用zookeeper服务

分为standalone(开发中使用)/quorum(生产中使用)模式

## zookeeper客户端session

zookeeper客户端选取zookeeper集群中的某个节点建立session

zookeeper客户端可用主动关闭session

一段时间内zookeeper客户端未向zookeeper节点发送消息,zookeeper节点会自动关闭session

zookeeper客户端发现连接断开会自动重连

## zookeeper的quorum模式

集群中只能有一个leader节点,可以有多个follower节点,leader节点可以处理读写请求,follower节点只能处理读请求,
follower节点收到写请求会将请求转发到leader节点

## zookeeper保证的数据一致性

全局可线性写入:先到leader的请求先处理,leader决定写请求顺序

客户端FIFO顺序:来自给定客户端的请求,按发送请求顺序执行

## quorum模式zookeeper集群演示

1. 配置多个zookeeper的conofig文件(本机演示注意`dataDir`和`clientPort`不要冲突)

2. 每个config中配置多个server,格式如:
`server.1=127.0.0.1:3333:3334`代表`server.1=ip:quorum通信端口号:leader选举端口号`

3. 使用`zkServer.sh start-foreground configPath`启动多个zookeeper

4. 使用`zkClilsh -server ip1:port1,ip2:port2,ip3:port3`连接zookeeper集群
