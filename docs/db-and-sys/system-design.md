-   [System Design](#system-design)
    -   [Theory](#theory)
    -   [Practice](#practice)
        -   [Middleware](#middleware)
        -   [Storage](#storage)
        -   [Computation](#computation)
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

Practice
--------

-   DNS
-   CDN

### Middleware

-   Coordination
    -   semantics: strong availability + strong consistency + small data
    -   `zookeeper`
    -   `etcd`
-   Task Schedule
-   Message Queue
    -   semantics: at least once
    -   `kafka`
    -   `rabbitmq`
    -   `rocketmq`
-   Communication
    -   RPC
        -   challenge
            -   failure -\> semantics
            -   latency -\> aync
            -   memory access
        -   `dubbo`
            -   service register and query
            -   calling procedure
            -   load balancer
            -   fault toletance

### Storage

-   Filesystem
    -   Lustre
        -   `HPC`
    -   GlusterFS
        -   NAS NFS
    -   HDFS
        -   `Hadoop`
    -   Ceph
    -   Swift: RESTful
-   SQL
    -   OLAP
    -   OLTP
    -   `VoltDB`, `GreenPlum`
    -   `mycat` sharding
-   Aggregated NoSQL - Key-Value
    -   `Redis`: see [redis](./infra.md#redis)
-   Aggregated NoSQL - Column-Family
    -   `HBase`
    -   `Cassandra`
-   Aggregated NoSQL - Document
    -   `mongodb`
-   Graph NoSQL
    -   `Neo4j`

### Computation

-   Map-Reduce
    -   `hadoop`
-   In-Memory
    -   `Spark`
-   Streaming
    -   `storm`
    -   `flink`

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

### Redis

Redis内存数据库的内存指的是共享内存么

-   Features
    -   complex data types built upon key-value engine
    -   in-memory but persistent
        -   fast even single-threaded: use single-threaded multiplexing
        -   on-disk snapshot
-   Data Type [link](https://redis.io/topics/data-types)
    -   Key-Value
        -   get, set
        -   getset, setnx (set-if-not-exist)
            -   `SETNX Lock TimeStamp`
    -   List
        -   pop, push, range, blpop (block until pop)
    -   Set
        -   add, members
        -   inter, union
    -   Sorted Set (associate score with value)
        -   add (score, value), score, incrby, rank, range
    -   Hash
        -   set, getall, incrby, keys
-   Tech Notes
    -   Cache Eviction [link](http://antirez.com/news/109)
        -   LRU
            -   ramdom sampling
            -   ramdom sampling with pooling
        -   LFU
            -   logarithmic counter to prevent overflow
            -   maintain last-access timestamp to halve the counter
            -   new key has count of 5 to survive
    -   Storage
        -   Eventual Persistency
            -   snapshot by daemon process
            -   change-log by append-only file
        -   Internal Type: dynamic string
        -   Memory Management: virtual memory layer
    -   Event-driven and IO Multiplexing
        -   redis use single thread to provide high-available service, Reactor model
        -   events
            -   file event: user socket
            -   time event: snapshot, expiration
    -   Expiration feature
        -   passive method: expire on access
        -   active method: periodically test random subset of expire set
    -   Pipelining
        -   Motivation
            -   reduce latency due to RTT
            -   reduce network IO context switch
    -   Transaction
        -   Procedure
            -   `MULTI` to start
            -   Queue: error is detected by client
            -   Discard: clear queue
            -   Execute:
                -   null reply: fail due to concurrent access
                -   bunk reply: error will not stop the execution of
                    other commands
                    -   insight: Redis only error when logical error,
                        which isn't solvable by rollback
        -   CAS by `WATCH`: make `EXEC` conditional
            -   `WATCH` will monitor modification to certain key
            -   `EXEC` will exec only if `WATCH`ed keys are not modified
                (else return null)
-   Practice
    -   Distributed Deployment
        ([link](https://redis.io/topics/cluster-spec))
        -   hash-tagged partition
            -   full-connected with Gossip ping-pong
            -   `MOVED` reply to redirect client request
        -   master-slave to ensure availability
        -   redis-sentinel to provide failover service
    -   Distributed Lock
        -   tradition
            -   lock key with expiration
            -   unsafe due to async replication
        -   [`RedLock`](https://redis.io/topics/distlock)
            -   signed lock: (Lock, RandomSignature)
                -   to avoid unlock when expired
            -   try lock all masters, rollback if timeout or minority
            -   Loophole
                -   process pausing: request -\> gc -\> lock expire -\>
                    get stale response
                -   clock skew: request -\> ABC grant -\> C early expire
                    -\> CDE form majority
                -   fencing token: use monotonic token to reject expired
                    request
    -   LRU Cache
        -   `maxmemory`
        -   Eviction Policy
            -   noeviction: throws
            -   allkeys-lru
            -   volatile-lru: lru among to-expire set
            -   volatile-ttl: ttl of to-expire key
            -   xxx-lfu
            -   xxx-random
    -   Secondary Index ([link](https://redis.io/topics/indexes))
    -   Cache Filter: defend malicious cache miss attack
        -   bloom filter to reject non-existing key
-   More
    -   [CS-Notes](https://github.com/CyC2018/CS-Notes/blob/master/docs/notes/Redis.md)

### MySQL

-   Transaction (InnoDB)
    -   Transaction Overview
        -   undo log: tuple, for rollback, for mvcc
        -   redo log: page, for recovery
        -   update process
            -   undo log = tuple delta, send to rollback segment
            -   redo log of rollback segment
            -   update tuple
            -   redo log of update
            -   mark dirty
        -   commit process
            -   flush redo log
        -   recovery process
            -   read page (possiblly with help of doublewrite)
            -   exec redo log
            -   exec undo log
    -   Isolation (from strong to weak)
        -   `SERIALIZABLE`: update on same value is serialized
            -   avoid A.select; B.insert.commit; A.select; (phantom read)
            -   **impl**: two-phase lock
        -   `REPEATABLE_READ` (default): read from origin state
            -   avoid A.read; B.write.commit; A.read; (non-repeatable read)
            -   **impl**
                -   in fact is `SNAPSHOT` level, no phantom read
                -   use read-view no read lock, write use **gap lock**
                -   write skew problem, goto system design
        -   `READ_COMMITED`
            -   avoid A.read; B.write; A.read; (dirty read)
            -   **impl**: command-level read-view, only record lock
        -   `READ_UNCOMMITED`
    -   MVCC
        -   multiversion record
            -   record = row\_header + payload
            -   row\_header = transaction\_id + rollback\_ptr
            -   rollback\_ptr -\> undo record in Rollback Segment
        -   read view
            -   creator id
            -   low\_limit: unborned transaction
            -   up\_limit: finished transaction
            -   ids: uncommitted transaction
    -   Redo Logging
        -   Mini Transaction = lock + write (data and redo log) + commit (flush log and unlock)
        -   Redo Log File
            -   circular array with fixed-len files = FileHeader + 4 Blocks [BlockHeader + Content + BlockTrailer]
            -   lock-free append
                -   sequence number determines write area in log buffer
                -   background thread refreshing updates in log buffer to page cache
            -   312 byte unit, no need for doublewrite
    -   Double Write: avoid partial write
        -   sequential doublewrite buffer (2 MB)
            -   allocation: alloc(32 page + 128 page) to exaust fragment array first
        -   grouped write
            -   reduce `fsync()` called
    -   Lock and Deadlock
        -   record lock
            -   implemented by primary index
            -   only query with primary index in use will trigger record lock, or fallback to table lock
-   Storage Engine
    -   MyISAM: good for analysis
        -   index: 非聚簇索引, store pointer at B+ leaf
            -   lazy index update
            -   table compression
            -   no foreign key
        -   concurrent
            -   table-level lock
            -   no transaction, no recovery
    -   InnoDB
        -   index: 聚簇索引, secondary index lookup primary key as
            pointer
            -   fulltext index since 5.6
        -   concurrent
            -   row-level lock
                -   record lock
                -   gap lock: lock range, avoid phantom read
                -   next-key lock: lock range and record
            -   transaction and recovery
                -   mvcc
-   Storage Structure
    -   page: doubly-linked list
        -   page directory: primary-key -\> record pointer
        -   user records: singly-linked list
-   Sharding
    -   read / write partition
    -   horizontal partition
        -   key-based
        -   range-based
        -   use external table as mapping function: flexibility
-   Replication ([link](https://zhuanlan.zhihu.com/p/35471971))
    -   motivation: read / write request seperation
    -   binlog
        -   log format
            -   row-level
                -   large amount
            -   statement level
                -   needs context
                -   randomness: time, trigger
        -   log relay: by file offset
    -   GTID
        -   format: `source_server: transaction_id`
-   Resource
    -   [Jeremy Cole's blog](https://blog.jcole.us/innodb/)

### Serialize Protocol

-   Format
    -   JSON
    -   XML
-   Engine
    -   Protobuf
    -   Boost
    -   MFC Serialization
    -   .Net
