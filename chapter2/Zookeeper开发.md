# ZooKeeper开发

## ZooKeeperAPI的使用

`Zookeeper`类组要方法:

```java
class Zookeeper {
    Zookeeper(connectString, sessionTimeout, watcher);
    create(path,data,flag);
    delete(path,version);
    exists(path,watch);
    getData(path,watch);
    setData(path,data,version);
    getChildren(paht,watch);
    sync(path);
}
```

说明:

1. 所有读取znode的API都可以设置watcher
2. 所有跟新zonde的API都有两个版本:无条件更新(version=-1)/有条件更新
3. 所有方法都有同步异步两个版本

异常处理:

1. KeeperException
2. InterruptedExcetion

## watch机制

依赖倒置/控制翻转

## ZooKeeperAPI: watch示例

1. 使用`zkEnv.sh`设置`CLASSPATH`

2. 看代码示例吧

## 使用zZooKeeperAPI实现分布式队列

### 设计`DistributedQueue`

1. 使用`/queue`的znode节点下的节点(顺序持久化的节点)表示队列中的元素

2. `offer`方法,在znode下创建顺序持久化的节点

3. `element`方法,获取队头节点

4. `remove`方法,删除队头节点

## 使用ZooKeeperAPI实现分布式锁(WriteLock)

通过使用`WriteLock`的API实现分布式锁,参考`WriteLockTest`源码

```java
class WriteLock {
    // 通过创建节点获取锁资源,如果前面没有锁则获取到,否则watch前一节点,在watch中监控前一节点的删除,监控到则重新获取锁
    boolean lock();
    // 释放锁,删除锁节点
    boolean unlock();
}
```

## 使用ZooKeeperAPI实现选举

```java
/** 类似分布式锁的实现 */
class LeaderElectionSupport {
    void start();
    void stop();
}
```

## 使用Apache Curator简化ZooKeeper开发

### Curator技术栈

1. Client:
2. Framework
3. Recipes
4. Extensions

提供了fluent风格API

### Curator的
