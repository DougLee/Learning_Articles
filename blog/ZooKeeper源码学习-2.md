title: "ZooKeeper源码学习(2) ZooKeeper的启动过程"
date: 2016-01-04 14:14:39
tags:
- Zookeeper
- Java
---

## ZooKeeper启动方法

服务端启动：bin/zkServer.cmd(windows)

其中，zkServer.cmd的代码如下

```shell

setlocal
call "%~dp0zkEnv.cmd"

set ZOOMAIN=org.apache.zookeeper.server.quorum.QuorumPeerMain
echo on
call %JAVA% "-Dzookeeper.log.dir=%ZOO_LOG_DIR%" "-Dzookeeper.root.logger=%ZOO_LOG4J_PROP%" -cp "%CLASSPATH%" %ZOOMAIN% "%ZOOCFG%" %*

endlocal

```
<!-- more -->
## ZooKeeper启动过程及源码分析

先放图，
![](/image/zookeeper2/1.jpg)

1. ZooKeeper启动入口，从zkServer.cmd中可以看出启动类为**org.apache.zookeeper.server.quorum.QuorumPeerMain**

```java
public static void main(String[] args) {
    QuorumPeerMain main = new QuorumPeerMain();
    try {
        main.initializeAndRun(args);
    } catch (IllegalArgumentException e) {
        LOG.error("Invalid arguments, exiting abnormally", e);
        LOG.info(USAGE);
        System.err.println(USAGE);
        System.exit(2);
    } catch (ConfigException e) {
        LOG.error("Invalid config, exiting abnormally", e);
        System.err.println("Invalid config, exiting abnormally");
        System.exit(2);
    } catch (Exception e) {
        LOG.error("Unexpected exception, exiting abnormally", e);
        System.exit(1);
    }
    LOG.info("Exiting normally");
    System.exit(0);
}
```

2. 解析配置文件，即zoo.cfg和myid

```java
DatadirCleanupManager purgeMgr = new DatadirCleanupManager(config
        .getDataDir(), config.getDataLogDir(), config
        .getSnapRetainCount(), config.getPurgeInterval());
purgeMgr.start();
    
if (args.length == 1 && config.servers.size() > 0) {
    runFromConfig(config);
} else {
    LOG.warn("Either no config or no quorum defined in config, running "
            + " in standalone mode");
    // there is only server in the quorum -- run as standalone
    ZooKeeperServerMain.main(args);
}
```

3. runFromConfig: 看runFromConfig的代码可以看出,代码跳转到了QuorumPeer，

```java
public void runFromConfig(QuorumPeerConfig config) throws IOException {
  try {
      ManagedUtil.registerLog4jMBeans();
  } catch (JMException e) {
      LOG.warn("Unable to register log4j JMX control", e);
  }

  LOG.info("Starting quorum peer");
  try {
      ServerCnxnFactory cnxnFactory = ServerCnxnFactory.createFactory();
      cnxnFactory.configure(config.getClientPortAddress(),
                            config.getMaxClientCnxns());

      quorumPeer = new QuorumPeer();
      quorumPeer.setClientPortAddress(config.getClientPortAddress());
      quorumPeer.setTxnFactory(new FileTxnSnapLog(
                  new File(config.getDataLogDir()),
                  new File(config.getDataDir())));
      quorumPeer.setQuorumPeers(config.getServers());
      quorumPeer.setElectionType(config.getElectionAlg());
      quorumPeer.setMyid(config.getServerId());
      quorumPeer.setTickTime(config.getTickTime());
      quorumPeer.setMinSessionTimeout(config.getMinSessionTimeout());
      quorumPeer.setMaxSessionTimeout(config.getMaxSessionTimeout());
      quorumPeer.setInitLimit(config.getInitLimit());
      quorumPeer.setSyncLimit(config.getSyncLimit());
      quorumPeer.setQuorumVerifier(config.getQuorumVerifier());
      quorumPeer.setCnxnFactory(cnxnFactory);
      quorumPeer.setZKDatabase(new ZKDatabase(quorumPeer.getTxnFactory()));
      quorumPeer.setLearnerType(config.getPeerType());
      quorumPeer.setSyncEnabled(config.getSyncEnabled());
      quorumPeer.setQuorumListenOnAllIPs(config.getQuorumListenOnAllIPs());

      quorumPeer.start();
      quorumPeer.join();
  } catch (InterruptedException e) {
      // warn, but generally this is ok
      LOG.warn("Quorum Peer interrupted", e);
  }
}
```

QuorumPeer的start方法
```java
@Override
public synchronized void start() {
    loadDataBase();
    cnxnFactory.start();        
    startLeaderElection();
    super.start();
}
```
QuorumPeer继承了Thread类,所以会转到QuorumPeer.run(),
QuorumPeer的run方法
```java
...
while (running) {
    switch (getPeerState()) {
    case LOOKING:
        LOG.info("LOOKING");

        if (Boolean.getBoolean("readonlymode.enabled")) {
            LOG.info("Attempting to start ReadOnlyZooKeeperServer");

            // Create read-only server but don't start it immediately
            final ReadOnlyZooKeeperServer roZk = new ReadOnlyZooKeeperServer(
                    logFactory, this,
                    new ZooKeeperServer.BasicDataTreeBuilder(),
                    this.zkDb);
    
            // Instead of starting roZk immediately, wait some grace
            // period before we decide we're partitioned.
            //
            // Thread is used here because otherwise it would require
            // changes in each of election strategy classes which is
            // unnecessary code coupling.
            Thread roZkMgr = new Thread() {
                public void run() {
                    try {
                        // lower-bound grace period to 2 secs
                        sleep(Math.max(2000, tickTime));
                        if (ServerState.LOOKING.equals(getPeerState())) {
                            roZk.startup();
                        }
                    } catch (InterruptedException e) {
                        LOG.info("Interrupted while attempting to start ReadOnlyZooKeeperServer, not started");
                    } catch (Exception e) {
                        LOG.error("FAILED to start ReadOnlyZooKeeperServer", e);
                    }
                }
            };
            try {
                roZkMgr.start();
                setBCVote(null);
                setCurrentVote(makeLEStrategy().lookForLeader());
            } catch (Exception e) {
                LOG.warn("Unexpected exception",e);
                setPeerState(ServerState.LOOKING);
            } finally {
                // If the thread is in the the grace period, interrupt
                // to come out of waiting.
                roZkMgr.interrupt();
                roZk.shutdown();
            }
        } else {
            try {
                setBCVote(null);
                setCurrentVote(makeLEStrategy().lookForLeader());
            } catch (Exception e) {
                LOG.warn("Unexpected exception", e);
                setPeerState(ServerState.LOOKING);
            }
        }
        break;
    case OBSERVING:
        try {
            LOG.info("OBSERVING");
            setObserver(makeObserver(logFactory));
            observer.observeLeader();
        } catch (Exception e) {
            LOG.warn("Unexpected exception",e );                        
        } finally {
            observer.shutdown();
            setObserver(null);
            setPeerState(ServerState.LOOKING);
        }
        break;
    case FOLLOWING:
        try {
            LOG.info("FOLLOWING");
            setFollower(makeFollower(logFactory));
            follower.followLeader();
        } catch (Exception e) {
            LOG.warn("Unexpected exception",e);
        } finally {
            follower.shutdown();
            setFollower(null);
            setPeerState(ServerState.LOOKING);
        }
        break;
    case LEADING:
        LOG.info("LEADING");
        try {
            setLeader(makeLeader(logFactory));
            leader.lead();
            setLeader(null);
        } catch (Exception e) {
            LOG.warn("Unexpected exception",e);
        } finally {
            if (leader != null) {
                leader.shutdown("Forcing shutdown");
                setLeader(null);
            }
            setPeerState(ServerState.LOOKING);
        }
        break;
    }
}
...
```

从run方法中可以清晰的看出来ZooKeeper中的节点有四种状态:

* LOOKING: 正在搜寻Leader
* OBSERVING: Leader已选出，响应客户端请求，不参与投票
* FOLLOWING: Leader已选出，响应客户端请求，并参与投票
* LEADING: 当前Server即为Leader

所有节点，初始化时会由getPeerState()方法中state初始化为LOOKING，因此每一个节点启动时的状态都是LOOKING。
下一步，就是参与投票，选出ZooKeeper集群的Leader，leader选举这一步非常重要，也是zookeeper里最复杂、最精华的一部分。