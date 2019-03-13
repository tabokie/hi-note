-   [Database](#database)
    -   [Redis](#redis)
    -   [MySQL](#mysql)
    -   [Serialize Protocol](#serialize-protocol)
-   [Framework](#framework)
    -   [Spring](#spring)

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
            -   reduce network IO context switch\
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

-   Transaction
    -   Isolation
        -   `SERIALIZABLE`: update on same value is serialized
            -   avoid A.select; B.insert.commit; A.select;
            -   **impl**: two-phase lock
        -   `REPEATABLE_READ` (default): read from origin state
            -   avoid A.read; B.write.commit; A.read;
            -   **impl**
                -   in fact is `SNAPSHOT` level, no phantom read
                -   use read-view no read lock, write use **gap lock**
                -   write skew problem, see
                    [system-design](./system-design.md)
        -   `READ_COMMITED`
            -   avoid A.read; B.write; A.read;
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

### Serialize Protocol

-   Format
    -   JSON
    -   XML
-   Engine
    -   Protobuf
    -   Boost
    -   MFC Serialization
    -   .Net

Framework
---------

### Spring

-   AOP: Aspect Oriented Programming
    -   decouple peripheral service from controller
    -   implementation
        -   proxy: `ControllerProxy::{ service(); trueController(); }`
            -   dynamic proxy (Spring)
                -   handler interface
                    -   `Enhancer: Interface`
                    -   `Handler::invoke(Method)::{ this.method(); }`
                -   call parent impl by reflection
                    -   `Enhancer: Impl`
                    -   `Enhancer::intercept(Object, Method)::{ service(); reflect.superMethod(); }`
-   IOC: Inverse of Control
    -   inverse control principle
        -   before: request flow from high level to low level
        -   now: low-level is built directly from request
    -   dependency injection
        -   method to construct high-level from low-level object
        -   method
            -   `Higher(Lower)`: ctor injection
            -   `setLower(Lower)`: setter injection
            -   `setLower(Lower)` and `ConcreteLower: Lower`: interface
                injection
    -   IoC Container: factory from dependency
    -   Other Feature
        -   loosely coupled
        -   recursive dependency
    -   Initialization: XML -\> Resource -\> BeanDefinition -\>
        BeanFactory
    -   Bean
        -   type `@Scope("type")`
            -   singleton (default)
                -   lazy-init
            -   prototype (`new Bean()`)
                -   Spring not responsible for its destruction
            -   request (new at HTTP request)
            -   session (share in HTTP Session)
            -   globalSession
        -   lifetime
            -   create Bean
            -   property setting
                -   XXAware interface
            -   (a) BeanPostProcessor::postProcessBeforeInitialization

            -   @PostConstruct / InitializingBean::afterPropertySet /
                XML::init-method
            -   (a) BeanPostProcessor::postProcessAfterInitialization

            -   @PreDestroy / DisposableBean::destroy /
                XML::destroy-method
-   SpringMVC
    -   Procedure
        -   Controller: request -\> DispatcherServlet use HandlerMapping
            -\> Handler (controller)
        -   Execution: handler -\> HandlerAdapter -\> execute -\>
            produce ModelAndView (data and logical view)
        -   View: view -\> ViewResolver -\> lookup View
        -   Model: model -\> DispatcherServlet -\> View -\> client
    -   Annotation
    -   Interceptor (拦截器)
        -   `HandlerInterceptor`
            -   preHandle -\> boolean
            -   postHandle: after Handler execution
            -   afterCompletion: after all
