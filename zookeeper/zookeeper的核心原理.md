

目标:

1. 了解zookeeper以及zookeeper的设计猜想
2. zookeeper集群角色
3. 深入分析ZAB协议
4. 从源码层面分析leader选举的实现过程
5. 关于zookeeper的数据存储



 ## zookeeper的由来

集群使用中几个问题：

1. 各个节点的数据一致性
2. 怎么保证任务只在一个节点上执行
3. 某个节点挂掉了, 其他节点如何发现并接替任务
4. 存在共享资源. 互斥性, 安全性

总之 是需要一个分布式协调机制



zookeeper设计猜想

1. 防止单点故障

集群方案(leader, follower), 还能分担请求

2. 每个节点的数据是一致的 必须要有leader

leader, master

3. leader挂了怎么办, 数据如何恢复

选举机制, 数据恢复

4. 如何去保证数据一致性

分布式事务 2PC

结论 

1. ZAB来实现选举
2. 2pc做数据一致性



ZAB协议 (消息广播, 崩溃恢复)

leader选举: 启动时, 运行中崩溃后 

启动的时候选举:

1. 发送 myid zxid epoch给所有节点
2. 检查zxid, 较大为leader 投票
3. 检查myid, 较大为leader
4. 统计投票























