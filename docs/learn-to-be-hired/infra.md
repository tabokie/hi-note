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
    -   Storage
        -   Eventual Persistency
            -   snapshot by daemon process
            -   change-log by append-only file
        -   Internal Type: dynamic string
        -   Memory Management: virtual memory layer
    -   Expiration
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
                -   bunk reply: error will not stop the execution of other commands
                    -   insight: Redis only error when logical error, which isn't solvable by rollback
        -   CAS by `WATCH`: make `EXEC` conditional
            -   `WATCH` will monitor modification to certain key
            -   `EXEC` will exec only if `WATCH`ed keys are not modified (else return null)
-   Practice
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
            -   Loophole
                -   process pausing: request -> gc -> lock expire -> get stale response
                -   clock skew: request -> ABC grant -> C early expire -> CDE form majority
                -   fencing token: use monotonic token to reject expired request
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
