



### ZAB协议的设计思想 和在zk中的使用

### Leader选举

* zxid最大会被设置为leader
* epoch 32位
* myid(服务器id, sid)

选举有四种状态:

* LOOKING
* LEADING
* FOLLOWING
* OBSERVING