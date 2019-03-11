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
    -   larger atomic
    -   slow
-   isolation
    -   dependency tracking
-   durability
    -   cacheable logging

#### Crowd

-   node interface
-   node simulation
    -   discrete event simulate

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

### Tencent

#### Keen Security Lab

(19-02)

#### MySQL

(19-03)

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

**result-and-summary**

### Pony.ai

Job Experience
--------------

### Company Overview

#### PingCAP

-   job
    -   hr-interview =\> assignment =\> tech-interview
    -   history assignment
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

#### Netease

-   job
    -   strongly focus on project
    -   late interview mail

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
    -   only on-phone interview, set your phone rings!

#### Hulu

-   base at Beijing

#### Google

-   job
    -   universal employment
        -   = resume + algorithm
        -   [questions](./google-algorithm.md)

#### Microsoft

-   ATC
-   MSRA

### Build Open-Source Experience

-   Participate in Open-Source Project
    -   bug report
        -   format
        -   compatibility bug
    -   bug fix
    -   feature committer
-   Google Summer of Code
    -   organization selects
        -   3D Geometry & Rendering
            -   new gui
            -   surface reconstruction
            -   function extension
        -   Rust
        -   Go
    -   requirement
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
