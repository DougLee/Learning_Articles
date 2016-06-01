title: "ZooKeeper源码学习(3) Leader选举"
date: 2016-01-05 11:05:41
tags:
- ZooKeeper
- Java
---

## 引子
考虑这样一个问题：某个服务可以配置为多个实例共同构成一个集群对外提供服务。其每一个实例本地都存有冗余数据，每一个实例都可以直接对外提供读写服务。在这个集群中为了保证数据的一致性，需要有一个Leader来协调一些事务，如何确定哪一个实例是Leader？

难点在于：

* 没有一个仲裁者来选定Leader
* 每一个实例本地可能已经存在数据，不确定哪个实例上的数据是最新的

分布式选举算法就是用来解决这个问题的。

## 主要流程


## 名词解释

* zxid(zookeeper transtion id): zookeeper事务id，即每条消息的id,是选举过程中能否成为Leader的关键因素，它决定当前Server要将自己这一票投给谁。zxid是一个64位数字，高32位为Leader信息又称为epoch，每次Leader转换时递增，低32位为消息编号，Leader转换时应该从0开始编号。
* myid/id(zookeeper server id): 她也是能否成为Leader的一个因素
* epoch/logicalclock: 主要用于描述Leader是否已经改变，每一个Server中启动都会有一个epoch，初始值为0，当开始新的一次选举时epoch加1，选举完成时epoch加1.

## 小结
Leader选举是ZooKeeper中最重要的部分之一。
