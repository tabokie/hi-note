-   [Database Product](#database-product)
    -   [Redis](#redis)
    -   [MySQL](#mysql)

Database Product
================

Redis
-----

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
        -   redis use single thread to provide high-available service,
            Reactor model
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

MySQL
-----

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
            -   avoid A.select; B.insert.commit; A.select; (phantom
                read)
            -   **impl**: two-phase lock
        -   `REPEATABLE_READ` (default): read from origin state
            -   avoid A.read; B.write.commit; A.read; (non-repeatable
                read)
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
        -   Mini Transaction = lock + write (data and redo log) + commit
            (flush log and unlock)
        -   Redo Log File
            -   circular array with fixed-len files = FileHeader + 4
                Blocks \[BlockHeader + Content + BlockTrailer\]
            -   lock-free append
                -   sequence number determines write area in log buffer
                -   background thread refreshing updates in log buffer
                    to page cache
            -   312 byte unit, no need for doublewrite
    -   Double Write: avoid partial write
        -   sequential doublewrite buffer (2 MB)
            -   allocation: alloc(32 page + 128 page) to exaust fragment
                array first
        -   grouped write
            -   reduce `fsync()` called
    -   Lock and Deadlock
        -   record lock
            -   implemented by primary index
            -   only query with primary index in use will trigger record
                lock, or fallback to table lock
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
