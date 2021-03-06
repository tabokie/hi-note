-   [System Design](#system-design)
    -   [Theory](#theory)
    -   [Design](#design)
        -   [Overview](#overview)
        -   [Design by Component](#design-by-component)
        -   [Design by Request](#design-by-request)
        -   [Design by Example](#design-by-example)

System Design
=============

Theory
------

-   Motivation of distributed system
    -   scale up beyond single machine
    -   tolerant single point failure
-   Theory
    -   CAP
    -   ACID
        -   Isolation ([link](https://www.cnblogs.com/ivan-uno/p/8274355.html))
            -   Basic
                -   `Serializable`
                -   `Repeatable Read`
                -   `Read Committed`
                -   `Read Uncommitted`
            -   Phenomenon
                -   Dirty Read
                -   Non-Repeatable Read
                -   Phantom Read
            -   Critique
                -   Lost Update (rr): A.read; B.read; A.write(ref+1).commit; B.write(ref+2).commit;
                -   Read Skew (rc)
                -   Write Skew: A.check; B.check; A.operate; B.operate; C.check.failed;
    -   BASE

    <!-- -->

        1.以Leader选举为主的一类算法，比如paxos、viewstamp，就是现在zookeeper、Chuby等工具的主体
        2.以分布式事务为主的一类主要是二段提交，这些分布式数据库管理器及数据库都支持
        3.以若一致性为主的，主要代表是Cassandra的W、R、N可调节的一致性
        4.以租赁机制为主的，主要是一些分布式锁的概念，目前还没有看到纯粹“分布式”锁的实现

-   Model
    -   Failure Model
        -   fail-stop
        -   fail-slow
        -   byzantine
    -   webserver

### Consensus Algorithm

-   2PC
-   Paxos
-   Raft
    -   Election Process
        -   election timeout: miss `AppendEntries` from leader
    -   when add node
-   ZAB
-   Eventual Consistency
    -   Gossip (`Cassandra`, aka Anti-Entropy, @Efficient Reconciliation and Flow Control for Anti-Entropy Protocols, @Epidemic Algorithms for Replicated Database Maintenance)
        -   Operation
            -   push(key, value, version) to node
            -   pull(key, version) from node
            -   push-pull(key, version): pull then push
        -   Protocol: Anti-Entropy
            -   SI = Suspective & Infective
            -   synchronize when inconsistent
        -   Protocol: Rumor-Mongering
            -   SIR = Suspective & Infective & Removed
            -   remove after interval
        -   Drawback
            -   delay
            -   redundant message
-   Fault Detection
    -   Heartbeat
        -   Drawback:
            -   sensitive to network jitter
            -   decentralized network
    -   Phi
        -   exponential distribution

### Transaction

-   TCC: try - confirm - cancel

Design
------

### Overview

-   the purpose is to explain **tradeoff** to accomplish specific task
-   and **showoff** your familiar tech stacks
-   procedure (from
    `cracking-the-system-design-interview-designing-pinterest-or-instagram-as-an-example`)
    -   requirement and specs
        -   function of the system
        -   scale of system data and request
    -   high-level design
        -   list the necessary components
    -   individual components and their interaction
    -   optional(estimation)

### Design by Component

-   Load Balancer
    -   Motivation
        -   Single Failure
        -   Resouce Balance and Horizontal Scaling
            -   stateless server
        -   Access Control
    -   Algorithm
        -   Round-Robin and Weighted RR
        -   Least-Busy
        -   session / cookies
        -   4-layer: transport layer
        -   7-layer: application layer
-   Reverse Proxy (contrast to forward-proxy on client, reverse-proxy is
    server-end dispatcher)
    -   Motivation
        -   Safety
        -   Scalability and Flexibility
        -   SSL Encapsulation
        -   Cache and Static Content
-   Web Server
    -   scaling (stateless)
        -   rps (request-per-second) and bandwidth
        -   vertical scaling (scaling-up)
        -   horizontal scaling (scaling-out)
    -   API
        -   MVC and MVVC
-   Application Service
    -   Service Locater
        -   `Zookeeper`, `etcd`: CP system
        -   `Dynamo`
    -   Micro Service
-   Data Store
    -   MySQL
        -   Scalability
            -   Horizontal Duplicate
                -   Master-Slave: slave is read-only
                -   Master-Master
            -   Functional Partition
            -   Sharding (Horizontal Partition)
    -   NoSQL
        -   Key-Value
        -   Document
        -   Column
        -   Graph
    -   RAID
        -   mirror
        -   stripe
        -   parity
        -   mirror + stripe
            -   virtual disk management
            -   1+0: stripe then mirror
                -   virtual disk = stripes
                -   single disk failure means one virtual stripe is
                    half-down
            -   0+1: mirror then stripe
                -   virtual disk = mirrors
                -   single disk failure means one virtual mirror failure
    -   Cache
        -   expiration: eventual consistency
-   Communication
    -   UDP / TCP
    -   HTTP
        -   REST API
    -   RPC

### Design by Request

-   Consistency
    -   distributed lock
        -   `zookeeper`'s ZAB protocol
        -   `redis`
        -   database record
    -   distributed id
        -   `zookeeper`
    -   file synchronize
    -   cache
        -   expiration: eventual consistency
        -   database then cache
            -   possible reorder
            -   waste of resource for write-intensive scenario
        -   delete then database
            -   double check: delete then update database finally expire cache after delay (if any)
                -   delay is determined by propagation time
                -   large for MySQL master-slave read-write cluster
        -   database then delete
-   Latency
    -   live video
        -   broadcast storm
-   Scalability / Availability / High Concurrency / Large Request
    -   methods
        -   horizontal duplication
        -   functional decomposition
        -   horizontal partitioning
-   High Concurrency
    -   asynchronous queue

### Design by Example

-   bidding race (秒杀系统)

            ELK
        6 怎么进行服务注册发现 zk实现具体说说

        7 数据库万级变成亿级，怎么处理。分库分表，分片规则hash和取余数。使用mycat中间件实现。
        5 你说了解分布式服务，那么你怎么理解分布式服务。
        13分布式的paxos和raft算法了解么

        paxos：多个proposer发请提议（每个提议有id+value），acceptor接受最新id的提议并把之前保留的提议返回。当超过半数的accetor返回某个提议时，此时要求value修改为propeser历史上最大值，propeser认为可以接受该提议，于是广播给每个acceptor，acceptor发现该提议和自己保存的一致，于是接受该提议并且learner同步该提议。

        raft：raft要求每个节点有一个选主的时间间隔，每过一个时间间隔向master发送心跳包，当心跳失败，该节点重新发起选主，当过半节点响应时则该节点当选主机，广播状态，然后以后继续下一轮选主。

        7 自己实现rpc应该怎么做

        redis的持久化方式，redis3.0原生集群和redis读写分离+哨兵机制区别

        ACID CAP BASE, C difference
        consistency hash ???