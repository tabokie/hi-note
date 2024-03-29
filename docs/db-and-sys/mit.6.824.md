-   [MIT - 6.824 Course Note](#mit---6.824-course-note)
    -   [Lecture 1](#lecture-1)
    -   [Lecture 2](#lecture-2)
    -   [Lecture 3](#lecture-3)
    -   [Lecture 4](#lecture-4)
    -   [Lecture 5-6](#lecture-5-6)

MIT - 6.824 Course Note
=======================

MIT 6.824 2017
[(site)](http://nil.csail.mit.edu/6.824/2017/schedule.html)

Lecture 1
---------

-   Why distributed?
    -   to connect physically separate entities
    -   to achieve security via isolation
    -   to tolerate faults via replication
    -   to scale up throughput via parallel CPUs/mem/disk/net
-   Topics
    -   Implementation
    -   Performance
    -   Fault-Tolerance
    -   Consistency
-   MapReduce
    -   context: multi-hour computations on multi-terabyte data-sets
    -   Feature
        -   Simplicity
        -   Scalability
    -   Detail
        -   Assume `fail-stop` server
        -   User-defined `map` and `reduce` are state-less
            -   easy to recover locally
        -   Input Data is partitioned into tasks, number \>\> server
            -   good for balance
        -   Input Data stored in GFS with 3 replicas
        -   Mapper read replica from local disk
        -   Mapper computes
            -   Master will ping to check liveness
            -   re-run if reducer hasn't fetch all mapped data
        -   Mapper write output files into local disk
        -   Reducer request intermediate file from all Mappers
        -   Reducer computes
            -   atomic rename to avoid partial failure and duplicate
                reducers

Lecture 2
---------

-   RPC
    -   Marshalling
    -   Binding
    -   Failure
        -   `at least once`
        -   `at most once`: detect duplicate request
            -   XID generation
            -   one at a time: server discard \# \< seq
            -   retry limit
        -   `exactly once`: at-most-once + unbounded retry +
            fault-tolerance

Lecture 3
---------

-   GFS
    -   Design
        -   Chunk: 64 MB with 3 replicas and 64 byte metadata
            -   fault-tolerant
            -   load balance
            -   size is big
        -   Master: in-memory metadata of dir and file with standby
            shadow (stale)
            -   fast response
    -   Operation
        -   read:
            -   get server list (addr, version) from Master
            -   ask nearest server
            -   retry if version number is wrong
        -   append:
            -   get servers (3 replica) from Master
            -   push to chained replicas
            -   contact primary:
                -   seq ++
                -   apply change
                -   reqeust replicas
                -   reply to client after all acks
                -   contect master
    -   Consistency
        -   directory is strong consistent, but master failure will
            result in stale directory
        -   file is weak consistent, hole or duplicate when retry,
            concurrent write if not atomic append
            -   use file rename for atomic append ??
            -   identifier for valid record
            -   read whole file instead of read by offset
    -   Misc
        -   COW Snapshot by reference count

Lecture 4
---------

-   Primary-Backup Replication
    -   Synchronize Method
        -   State Transfer
        -   State Machine
    -   State = uniprocessor machine
        -   Model
            -   share disk
            -   client-server and log channel
            -   input includes clock/disk/network interrupt
        -   Deterministic Replay
            -   event logging with volatile register value stored
            -   output is idempotent
        -   FT protocol
            -   primary buffer output until backup's output logged and
                ack
        -   Shared disk as reliable log channel

Lecture 5-6
-----------

-   Raft
    -   why majority
        -   partition: split brain
        -   fault-tolerant: preserve data through majority mutation
    -   why log
        -   execution order
        -   operation replay
    -   Commit
        -   leader forward request to replicas
        -   if majority append log, leader commit
        -   majority run operation
        -   leader replies
    -   Election
        -   term id as logical clock
        -   trigger: leader heartbeat and random timeout
        -   voting: 
            -   request carry last record term-id and index, only vote for newer record
            -   first come first serve
    -   Log Synchronization
        -   new leader has newest log, or it wont have large enough term-id
        -   those with stale term-id has missing logs, cant become leader.
        -   leader use backtracing to synchronize log to all followers
    -   Amendment
        -   a hidden new term comes back to override committed log
            -   term 3: A log term 3 request and down
            -   term 4: B sync term 2 log to majority and down
            -   term 5: A use term 3 request to override the term 2 committed log
        -   leader node can only commit logs containing current term
            -   so that a hidden new term wont be leader again
    -   Two-Phase topological mutation
        -   first phase: old + new to old and to new ( not to n-old + n-new )
            -   if commit, new leader must have old + new topo and majority of old + new
        -   second phase: new to new

Lecture 7
---------

Lecture 8
---------

-   Zookeeper
    -   Motivation: generic master service
        -   master acting as coordinator
        -   fault-tolerent
        -   high-performance