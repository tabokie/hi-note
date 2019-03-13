-   [Database](#database)
    -   [Redis](#redis)
    -   [MySQL](#mysql)
    -   [Serialize Protocol](#serialize-protocol)

Database
--------

### Redis

-   Features
    -   complex data types built upon key-value engine
    -   in-memory but persistent
        -   fast even single-threaded
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
    -   Eventual Persistency
        -   dump to disk
        -   change-log
    -   Internal Type: dynamic string
    -   Memory Management: virtual memory layer
    -   Redis Expiration
        -   passive method: expire on access
        -   active method: periodically test random subset of expire set
    -   Distributed Deployment ([link](https://redis.io/topics/cluster-spec))
        -   hash-tagged partition
            -   full-connected with Gossip ping-pong
            -   `MOVED` reply to redirect client request
        -   master-slave to ensure availability
    -   Distributed Lock
        -   tradition
            -   lock key with expiration
            -   unsafe due to async replication
        -   [`RedLock`](https://redis.io/topics/distlock)
            -   signed lock: (Lock, RandomSignature)
                -   to avoid unlock when expired
            -   try lock all masters, rollback if timeout or minority

### MySQL

-   Index
    -   first index
    -   second index
-   Transaction
    -   Isolation
        -   level, default, implementation

### Serialize Protocol

-   JSON
-   Protobuf

## Framework

### Spring

-   Single Instance Pattern
-   循环依赖
-   Bean lifetime
-   AOP
    -   implementation
-   IOC
-   拦截器
-   MVC
    -   Annotation
