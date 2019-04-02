-   [Distributed System Case Study](#distributed-system-case-study)
    -   [Practice](#practice)
        -   [Middleware](#middleware)
        -   [Storage](#storage)
        -   [Computation](#computation)

Distributed System Case Study
=============================

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
