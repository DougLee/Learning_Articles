# 分布式计算的基础 MapReduce



## MapReduce简介

源自于2004年Google发布的论文, 是一种大规模数据处理的编程模型

MapReduce in Hadoop, 开源社区实现版本, 核心代码使用Java实现.



### MapReduce的计算场景

* 数据查找
  * 分布式Grep
* Web访问日志分析
  * 词频统计
  * 网站PV UV统计
  * TOP K问题
* 倒排索引
  * 建立搜索引擎索引
* 分布式排序

### MapReduce的优缺点

* 优点
  * 模型简单
    * Map + Reduce
  * 高伸缩性
    * 支持横向扩展
  * 灵活
    * 可以处理非结构化的数据
  * 速度快
    * 高吞吐离线处理数据
  * 并行处理
    * 编程模型天然支持并行处理
  * 容错能力强
* 缺点
  * 不支持流式数据
  * 不适合实时计算
  * 不适合复杂算法
  * 不适合迭代计算

## MapReduce编程模型

### 如何统计一个文本单词的出现次数

* Bash命令实现
* 单机版实现
* 如果数据量极大如何在分布式机器上实现?

### MapReduce使用了分治思想简化计算处理模型为两步

* Map阶段
* Reduce阶段

### MapReduce -Shuffle

因为Reduce阶段的数据来源于不同的Map, 所以需要Shufle归并到一起. Shufle由Map端和Reduce端组成. Shulfe的核心机制: 数据分区 + 排序

Map端

对Map数据结果进行spill(溢写)

Reduce端

拷贝Map端输出结果到本地; 对拷贝的数据进行归并排序







## MapReduce Java API介绍





## MapReduce 执行机制



























