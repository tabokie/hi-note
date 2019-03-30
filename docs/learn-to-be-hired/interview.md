-   [Interview Record](#interview-record)
    -   [Project Summary](#project-summary)
        -   [Optix Ray Tracer](#optix-ray-tracer)
        -   [Coplus Parallel Library](#coplus-parallel-library)
        -   [sBase Database](#sbase-database)
        -   [SpecTM transactional memory](#spectm-transactional-memory)
        -   [Crowd](#crowd)
        -   [Portal-DB](#portal-db)
    -   [PingCAP](#pingcap)
        -   [Infra Intern](#infra-intern)
    -   [Bytedance](#bytedance)
        -   [Backend Develop Intern at
            EE](#backend-develop-intern-at-ee)
    -   [Alibaba](#alibaba)
        -   [Antfin Big Data Platform
            Intern](#antfin-big-data-platform-intern)
        -   [Antfin OceanBase](#antfin-oceanbase)
    -   [Tencent](#tencent)
        -   [Keen Security Lab](#keen-security-lab)
        -   [MySQL Group](#mysql-group)
        -   [WXG Search and
            Recommendation](#wxg-search-and-recommendation)
    -   [Netease](#netease)
        -   [LeiHuo Game Engine Intern](#leihuo-game-engine-intern)
-   [Job Experience](#job-experience)
    -   [Company Overview](#company-overview)
        -   [PingCAP](#pingcap-1)
        -   [Netease](#netease-1)
        -   [Bytedance](#bytedance-1)
        -   [Tencent](#tencent-1)
        -   [Alibaba](#alibaba-1)
        -   [Hulu](#hulu)
        -   [Google](#google)
        -   [Microsoft](#microsoft)
    -   [Experience Building for Tech
        Company](#experience-building-for-tech-company)
    -   [Transfer to USA](#transfer-to-usa)

Interview Record
----------------

### Project Summary

#### Optix Ray Tracer

-   problem
    -   bsdf material
    -   thousands of lights
    -   point cloud tracing
-   physically rendering
    -   multiple importance sampling
-   optimization
    -   point cloud mapping (preprocess pass)
        -   `ply` point cloud -\> float3 buffer -\> float2 buffer -\>
            memory
    -   intersection reuse
        -   first pass: intersection
            -   light-irrelavant data
                -   space coordianate
                -   surface coordinate: texcoord and normal
                -   attenuation
                -   material id
        -   second pass: shading
            -   reuse intersection
    -   machine learning denoiser
    -   cuda program optimization
        -   branch
        -   per-ray data

#### Coplus Parallel Library

-   `coroutine`
    -   state: `Waiting`, `Ready`, `Finished`
    -   context
        -   program context
        -   calling context: thread local store
        -   dependency context
    -   semantics
        -   ready queue: mpmc queue
        -   wait queue: immutable push-only stack
            -   id locatable
-   memory pool
    -   memory dyeing
    -   buddy algorithm
-   data structure
    -   slow multiple-consumer-multiple-producer queue
        -   performance by the cost of correctness
        -   rependent count to distribute pressure
        -   two CAS
            -   add top
            -   add rependent if failed
    -   fast multiple-consumer-multiple-producer queue
        -   divide-and-conquer: each producer get thread-local sub-queue
            -   distribute consumer pressure
            -   no race condition for producer
        -   free-list and queue-pool
        -   two CAS for consumer, mutex for producer handle
    -   push-only stack
        -   balance the need for variable-length structure
            -   fixed-length array \* fixed-length bucket
            -   bucket is lazy-initialized
        -   two CAS
            -   add top
            -   initialize bucket

#### sBase Database

-   storage engine
    -   B-Flow tree
-   concurrent control
    -   page-level

#### SpecTM transactional memory

-   motivation
    -   in concept: larger atomic
    -   in practice: slow for write-barrier and logging
-   isolation
    -   dependency tracking
-   durability
    -   cacheable logging

#### Crowd

-   node interface
    -   time
    -   network
    -   response
-   node simulation
    -   discrete event simulate
        -   concurrent priority queue
            -   skiplist-based
            -   optimize CAS operation by `delete` flag

#### Portal-DB

-   hash-trie
    -   merit
        -   fixed-length key prevent booming nodes
        -   combine fast query with sorted scan
        -   fast comparison and fast hash
    -   demerit (so far)
        -   inconsistent scan
        -   cold startup
-   trick
    -   endian inpair comparison
    -   rusty borrow

### PingCAP

#### Infra Intern

**hr-and-project**

    KV Server
    编写一个 C/S Demo。服务器上存储有一个有序的 Key-Value 集合（类似于 c++ 中的 std::map<string, string>）。要求：
    需要实现 Get，Put，Delete 和 Scan 接口
    Key 固定 8 字节，value 固定 256 字节
    （加分项）需要能对内存数据进行持久化保存，重启之后数据不丢失
     
    提示：
    1. 编程语言请使用 c/c++/go/rust。
    2. 设计协议时要考虑到返回的数据量可能会很大。
    3. 注意代码风格，添加必要的单元测试和文档。
    4. 注意异常处理，尝试优化性能。

**first-interview (mentor)**

-   project: sBase, portal-db
-   process and thread and coroutine
-   algorithm
    -   two stacks as a fifo
    -   quicksort and time complexity
-   Q&A
    -   TiKV with Rust and C++ (rocksdb)

**second-interview (moved-ahead, He)**

-   portal-db
    -   key = 8
        -   reason
            -   limited space
            -   less hash and comparison
    -   benchmark
        -   segment and mixed
        -   single and multi-thread
        -   mock of KV
    -   debug: Linux server running slow
        -   log + pattern -\> repete
        -   specific reason
            -   disk file
            -   deletion
-   heap
    -   operation
    -   top-k
    -   top-k-freq
        -   hash then heap
        -   use segmented hash and cache
        -   incremental update when repeatedly call
-   set
    -   operation: add, del, in, pick
    -   auxiliary vector pointed to hash table slot
        -   deletion
            -   rejection sampling with periodically resize
            -   stack-like with top, use swap to reduce resize
-   sBase
    -   B-Flow
        -   followup: LSMT
            -   scan operation
    -   SQL
    -   index: B/B+
    -   transaction: possible phenomenon
-   Rust / C++
-   distributed algorithm: Raft
    -   majority
    -   leader with 2 voter and 4 committer
    -   leader with 4 voter and 2 committer
        -   less availability and same consistency ?
-   department of TiKV
    -   Open-Source Raft (he in?)
    -   ACID transaction
    -   low-level engine
    -   computation push-down
-   Q&A: about your firm
    -   company's future has little influence
    -   freedom and flexibility
    -   better for improvement and realization because everyone is
        novice

**third-interview (USA-pre-facebook)**

### Bytedance

#### Backend Develop Intern at EE

(19-01)

**first-interview**

-   basics
    -   session and cookies
    -   Ctrl + C
    -   MySQL storage engine: InnoDB
    -   process communication
        -   how to use semaphore
-   distributed system
    -   design distributed transaction for two seperate services
        (balance and transfer record)
-   database
    -   index process
    -   design a index for {seller, customer, order}: primary customer,
        secondary customer\|order
        -   justify your design based on relative magnitude
-   algorithm (handwrite, no OJ)
    -   linked list intersection
        -   return bool
        -   return ListNode\*
    -   count number in a big file
    -   quicksort linked list

**second-interview**

-   basics
    -   HashMap in C++
    -   locks, spinlock
    -   CAS
    -   disk write operation, time, dirty page write-back
-   project
    -   about coroutine library, do you design memory management
    -   about database, what's your design to optimize sql, e.g. x in
        select \* from A
-   database
    -   leveldb, skiplist, lsmtree
-   algorithm (OJ platform but no AC required)
    -   find max-k number in 10G file
    -   find repeated number in 10G file
        -   bloom filter
    -   sort linked list where odd nodes incr and even nodes decr

**hr-interview**

-   your hobbies and your motivation on the path of CS
-   elaborate one of your experiences, how many people are involved
-   your understanding of bytedance and EE department
-   what you expect of your mentor during internship
-   can you accept overtime working

**result and summary**

offer at Hangzhou and transfer to Beijing for it's more system-related.

focus on concrete knowledge, less theory talk.

### Alibaba

#### Antfin Big Data Platform Intern

(19-02)

**pre-interview (19:00 on phone, 1 hour)**

-   project
    -   HTM, detail
    -   ray tracer, procedure
-   parallel programming
    -   shared access
        -   optimistic versus passive lock
    -   message queue
    -   scheduling
        -   context saver
-   distributed system
    -   distributed lock
        -   distributed cache
-   basic
    -   design pattern
    -   OOM
    -   network
        -   session and cookies
        -   errorno
-   overview
    -   restructure your code
    -   favorite class
    -   area you have touched
    -   why laboratory

**first-interview (20::00 on phone, 1 hour + 30 min)**

-   Java
    -   HashMap
        -   Node
        -   resize factor
        -   Java 1.8 version
        -   thread safety
            -   two condition
            -   ConcurrentHashMap
        -   retrieve by order: LinkedHashMap
    -   ThreadPool
        -   configuration
        -   `keepAliveTime`
        -   enqueue when
            -   corePoolSize
        -   corePoolSize configuration
            -   CPU
            -   daemon thread
            -   task property (IO / compute)
            -   more the better
                -   context switch
                -   memory
    -   synchronization
        -   lock / `synchronized`
        -   reentrant
        -   optimistic / pessimistic
            -   merits
    -   Spring IOC
-   Network
    -   Https
        -   why use two protocol
            -   can't create session by as ?
            -   random key
    -   `GET` and `POST`
        -   can `GET` be used to transfer data
    -   HTTP Header
    -   `Cookies` and `Session`
        -   motivation
        -   obtain session persistency in server cluster
            -   memory cache
            -   master manifest
-   System
    -   load balance
        -   RR and weighted RR
        -   consideration
            -   distance
            -   ability
            -   resource
    -   database
        -   inadequate connect number
        -   `select` optimization
            -   partition
-   Algorithm
    -   first 1000 in file
    -   FIFO and Priority
-   Dead End
    -   Linux

**result**

After communicating with interviewer, transfer to OceanBase.

#### Antfin OceanBase

(19-03)

**first-interview (1 hour)**

-   project: Database
    -   B-flow tree
        -   followup: LSM-tree
            -   why called level: compression
            -   why leveled: compression
        -   followp: OceanBase
    -   record serialization and disk write
    -   write disk timing
        -   commit
    -   write disk before commit (memory replacement)
        -   page version number
        -   followup
            -   new transaction with bigger version needs to persist
    -   MySQL log
        -   use log to solve that problem
        -   if memory is large, readback page and rollback
-   project: coplus
    -   coroutine merits
    -   coroutine implementation
        -   register: PC, temporary variable
        -   stack: Windows' help
    -   wait-free queue
-   c++
    -   virtual and abstract virtual
        -   mistake as virtual inheritance, fuck me
    -   virtual dtor
-   coding
    -   `segment "seg me'n t"more 'en d'`
    -   test case

**second-interview (YANG Chuanhui P10)**

-   project:sBase
    -   B-Flow
-   project: lab
    -   in-memory transaction log
-   project: coplus
    -   coroutine
-   project: Crowd
-   heap implementation
    -   linked list
    -   array
-   lru cache
    -   sampling, RR
    -   doubly-linked list
    -   thread safety?
        -   circular queue
-   about yourself
    -   plan
    -   merit and demerit

**hr-interview**

-   choice of DB
-   your edge against master
-   Q&A
    -   compare with PingCAP
        -   not open-source, but independent-product
        -   good DB takes years, not in a rush
        -   \~ Oracle, \~ Huawei Gauss
        -   used in bank, not Mobi of PingCAP, reliability
        -   best minds in DB area
    -   suggestion
        -   do not underestimate the hardwork of DB
        -   be careful and detailed
    -   wait for 4.10, the first batch of Offers

**result-and-summary**

-   best interview experience among big firms
    -   nicely touch deep, an impression of cool professionals
    -   all on phone, no pressure
    -   relatively quick response

### Tencent

#### Keen Security Lab

(19-02)

**pre-interview**

missing, mainly about CAS and memory.

#### MySQL Group

(19-03)

**first-interview**

-   project
    -   sBase
        -   followup
            -   B+ property
    -   coplus
        -   followup
            -   synchronize
            -   queue property
-   C++
    -   polymorphism implementation
        -   what is itw
-   Linux
    -   pipe
    -   top command
    -   syscall
    -   process
        -   state
        -   process and thread
-   Database
    -   join type

**result**

turn down for bad interview taste and dull work.

#### WXG Search and Recommendation

(19-03-19)

**first-interview**

-   coding
    -   `add(string, string, int base)`
        -   `delete NULL`
        -   `buffer[length_] = '\0'`
    -   `printRotateArray(int)`
    -   `class String`
    -   `Add(BigObject)`
        -   `unique_ptr`
        -   `LinkedVector` and `sort(ptrs)`
-   project
    -   coroutine library
        -   implementation
        -   merits
            -   IO intensive and CPU intensive
    -   database
        -   index
            -   explain `id, name, age`
            -   LSMT merits
            -   reflection
                -   ORM
-   intelligence
    -   3L and 5L to 4L
    -   1 heavy in 8: 2 times
-   question
    -   why WXG
        -   search and distributed storage
        -   self-dev
    -   intern to formal
        -   test before 8.30
    -   spoiler: group leader, manager, cross-interview, hr interview

(19-03-25)

**second-interview**

-   start programming
    -   new explosure: GPU
    -   challenge: no GPU product, workload optimization
-   sBase
    -   engine
        -   index stores file pointer
        -   B-Flow
    -   parallel
-   portal-db
    -   network: Windows multiplexing
    -   persist: snapshot + bin-log
        -   bin-log commit
-   Linux \[X\]
-   STL \[X\]
-   coplus
    -   why not use libco
        -   try application, not fundamental infra
-   Hadoop \[X\]
-   Java, Python
-   hobby
-   location

**hr-interview**

-   study with practice
    -   demate your own argument
-   merit and demerit
-   family
-   parent influence
-   hobby
-   success and failure in your college year
-   what if you start over your college life

**result-and-summary**

-   worst interview experience so far (19-03)
    -   self-righteous
-   hr interview is so\~ deep
-   offer in a week
    -   formal letter of commitment needed
    -   5000 with 2000 housing per month plus 2000 traffic
        (off-province)

### Netease

#### LeiHuo Game Engine Intern

(19-02)

**first-interview (two in a row)**

-   experience
    -   `CUDA ray tracing`: how you optimize
        -   answer: intersection cache
    -   `coplus`: merits
        -   answer: non-preemptive, no spinning
        -   followup
            -   context save
                -   answer: OS + ...
            -   do you know usage of coroutine in Game (private
                question)
            -   coroutine in Unity
            -   function stack
            -   performance bottleneck
                -   answer: CAS, context switch
-   parallel programming
    -   allocator
        -   answer: `coplus`
        -   followup: optimize in following three condition
            -   scoped variable
            -   jabbed
            -   global
-   graphics
    -   rendering
        -   forward, deferred, tile-based

**second-interview (cross)**

-   experence
    -   `ray tracing`
        -   followup
            -   bidirectional ray tracing
            -   importance sampling
-   graphics
    -   deferred render
        -   argue: for many-light condition, not for shading clipping
-   parallel programming
    -   linux wait-free queue
    -   concurrent queue in `coplus`
-   c++
    -   new / malloc
        -   new (addr) Obj()
    -   iterator
        -   container expand
        -   delete when iterating
            -   hint: `i = i.erase()`
        -   delete when container shrink
            -   answer: `i != v.end()`
    -   virtual function
        -   virtual dtor
        -   vtable
-   algorithm
    -   rotate-k (from beauty-of-code): boom

**third-interview**

-   experience
    -   ray tracing optimization
    -   concurrent queue
    -   followup
        -   shared object: spinlock, mutex, semaphore
        -   did you implement rasterizer
-   graphics
    -   ray intersect optimization
        -   8-ary tree: nope
    -   forward and deferred shading
    -   game engine
        -   unity, bullet
-   c++
    -   container
        -   followup: sort vector and list
        -   followup: partition in QuickSort
            -   random sample
            -   range feature
    -   typecast
    -   memory leak
        -   operating system
        -   allocator

**hr-interview (after a week)**

**result-and-summary**

-   offer after 1 week, 6000 per month ;(

Job Experience
--------------

### Company Overview

#### PingCAP

-   job
    -   hr-interview =\> assignment =\> tech-interview
    -   history assignment (intern-mixed)
        -   2018: 利用 kubectl plugin 机制实现一个插件，用于 debug
            任意一个 pod 里的容器，达到 kubectl exec 的使用体验.
            [github](https://github.com/aylei/kubectl-debug)
        -   2018-09/10: maintain system: design a concurrent task queue
            to put modification plan with safe freezing on exception.
            [github-1](https://github.com/xiaozzz/PingCAP),
            [github-2](https://github.com/deqianzou/PingCAP-TaskQueue)
        -   2018-10: design and implement a data structure for Least
            Recently Used (LRU) cache. It should support the following
            operations: get and put. O(1).
            [github](https://github.com/panxiande/PingCap)
        -   2018-10: design a realtime service that lookup city w.r.t.
            ip input. data 500 millions.
        -   2018-10: two servers with public ip at 33 locations which
            can download `pingcap.tar.gz(2GB)`. design a system for
            national download which is fast and fault-tolerant.
            [github](https://github.com/tianjiqx/PingCAPInterviewProject)
        -   2018-10: Design an index structure to minimize the cost of
            reading each key-value randomly and concurrently.
            Preprocessing allowed.
            [github](https://github.com/lightghli/ryougidb)
        -   2018-10/2019-02: use 1 GB memory to account top-100 of 100
            GB url file. [github-1](https://github.com/YiZhao0319/topK),
            [github-2](https://github.com/ltg001/URL_counter_mapreduce)
        -   2018-11: two csv tables t1(a) and t2(b) with million lines.
            design an algorithm for
            [github](https://github.com/fang19911030/PingCap)
            `select count(*) from t1 join t2 on t1.a = t2.a and t1.b > t2.b`,
            analyse options and test sensitivity to number of distinct
            values.
            [github](https://github.com/FishinLab/PingCAP_Interview)
        -   2018-12: 实现 TiDB 在 K8s 之上的 yaml 部署方案 a.使用 Local
            PV, b.要求 PD/TiKV/TiDB 使用配置文件,
            c.集群部署时能够自动设置 TiDB 密码, d.集群需要有
            Prometheus/Grafana 监控.
            [github](https://github.com/GSIL-Monitor/pingcap-mianshi)
        -   2019-02: two unordered table file(1 TB): (item\_id: u64,
            item\_price: u64) and (user\_id: u64. item\_id: u64).
            calculate user cost.
            [github](https://github.com/DQinYuan/pingcap_interview)
        -   2019-03: Index Structure for random access key-value
            [github](https://github.com/wxhwang/UnorderedFileIndex)
        -   2019-03: find first non-repeated word in 10 GB file
            [github](https://github.com/Qiwc/FirstNoReapting)
    -   my assignment: persistent key-value server (19-03)

#### Netease

-   job
    -   strongly focus on project
    -   late interview mail
    -   bad interview (Leihuo) : bad prep, long pauses

#### Bytedance

-   job
    -   no redundant department details
    -   efficient

#### Tencent

-   job
    -   awful experience
    -   wrong department holds on to your resume
    -   hr interview and offer call are extremely formal and detailed

#### Alibaba

-   tech
    -   language: Java
    -   department:
        -   `antfin` / `alipay`
        -   `aliyun`
        -   `data platform`
        -   `middleware`
-   job
    -   favor referee
    -   emphasize basic knowledge
    -   departments share same employ system
        -   one referral at a time
        -   includes cross interview
    -   freezing time more than 0.5 year
    -   mostly on-phone interview, set your phone rings!

#### Hulu

-   base at Beijing

#### Google

-   job
    -   universal employment
        -   = resume + algorithm
        -   [questions](./google-algorithm.md)

#### Microsoft

-   STCA: easy
-   MSRA

### Experience Building for Tech Company

-   Source Code Study
-   Hands-On Industrial Product
-   Open-Source
    -   Participate in Open-Source Project
        -   bug report
            -   format
            -   compatibility bug
        -   bug fix
        -   feature committer
    -   Google Summer of Code
        -   requirements
            -   participation
            -   proposal

### Transfer to USA

**Option A: Master Degree**

**Option B: Company Transfer**

-   L visa: bound to employer
-   Microsoft: senior level (2-3 year), internal interview, Bing team
    (base at Suzhou)
-   Hulu
-   Airbnb, Amazon, LinkedIn, Google

**Option C: Direct Apply**

-   h1b: at least Oct
-   temporary transfer (Australia)
-   options: Microsoft, Amazon, Pony.ai
