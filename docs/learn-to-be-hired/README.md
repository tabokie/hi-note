-   [Learn to be Hired](#learn-to-be-hired)
    -   [Network](#network)
        -   [Transport Layer](#transport-layer)
        -   [Application Layer](#application-layer)
    -   [Operating System & Linux](#operating-system-linux)
        -   [Memory](#memory)
        -   [Process](#process)
            -   [Process Management](#process-management)
            -   [Process Communication](#process-communication)
        -   [Network IO](#network-io)
        -   [Filesystem](#filesystem)
    -   [Language](#language)
        -   [C++](#c)
            -   [Memory](#memory-1)
            -   [Polymorphism](#polymorphism)
            -   [Multi-Thread](#multi-thread)
            -   [Meta Programming](#meta-programming)
            -   [Standard Library](#standard-library)
            -   [Misc](#misc)
        -   [Java](#java)
    -   [Database](#database)
    -   [Parallel Programming](#parallel-programming)
        -   [Paradigm](#paradigm)
    -   [Systems](#systems)
    -   [Algorithm](#algorithm)
        -   [BigData and Online
            Algorithm](#bigdata-and-online-algorithm)
    -   [Interview Record](#interview-record)
        -   [Bytedance](#bytedance)
            -   [19-01 Backend Develop Intern at
                EE](#backend-develop-intern-at-ee)
        -   [Tencent](#tencent)
            -   [19-2 Keen Security Lab](#keen-security-lab)
    -   [Job Experience](#job-experience)
        -   [Company Overview](#company-overview)
            -   [Hulu](#hulu)
        -   [Transfer to USA](#transfer-to-usa)

Learn to be Hired
=================

Network
-------

### Transport Layer

**HTTP connection**

-   HTTP/0.9 short connection
    -   each request = { DNS -\> three-step -\> transport -\> four-step
        }
-   HTTP/1.0 durable connection
    -   Connection Field = Keep-Alive / Close
    -   timeout = 5, max = 100
    -   Implementation
        -   1.  hold active processes until exaustion of pid

        -   2.  IO multuplexing

        -   3.  hold thread and buffer request until data is ready

    -   Application
        -   1.  response on request

        -   2.  (长轮询) on request, server will wait for data ready or
                timeout

        -   3.  (Comet HTTP stream) continously send data to client
-   HTTP/1.1 pipelined connection
    -   default Keep-Alice
    -   Head-of-Line-Blocking(HOLB) problem
        -   multiple connection single queue
-   HTTP/2 multiplexing
    -   stem from Google SPDY
    -   streamID, allow disorder and priority queue

**TCP**

-   Three-step Handshaking
    -   {SYN, seq=x} {SYN, ACK, seq=y, ack=x+1} {ACK, seq=x+1, ack=y+1}
    -   consensus on sequence number
-   Four-step Handshaking
    -   (a){FIN, seq=u} (b){ACK, seq=?, ack=u+1} ... (b){FIN, ACK,
        seq=w, ack=u+1} (a){ACK, seq=u+1, ack=w+1}
    -   time-wait: after a receive FIN and response to b, wait for 2 \*
        MSL
        -   to resend response if last one lost and server reclose
        -   wait for all old data vanish \<- connection is identified by
            quad {ip,port,ip,port}
-   data ack and sack
    -   receiver ack for last completed package, repeated ack number is
        considered a package loss
    -   sack will ack the specific package seq, also contains
        trouble-maker and recent block list \[a,b)
        -   server will maintain un-acked list and resend using sack
            info
    -   dsack

**UDP**

-   necessity
    -   TCP: loss of package must be recovered
    -   UDP: no retransmission
        -   timing and broadcast
        -   flexibility for application layer, fight network jitter and
            low bandwidth
    -   Case Study
        -   P2P (UDP)
            -   UDP is not connection-based, transmission is not
                contrained by quad
            -   therefore response can be sent from different port
        -   NAT by UDP
            -   TCP wraps UDP (reliable as a whole) is better than TCP
                wraps TCP
-   design reliable protocol on UDP
    -   principle: validity(real-time), reliability, fairness
    -   case study
        -   QUIC (Google's Quick UDP Internet Connections)
        -   TFTP (trivial ftp)
            -   seq and ack and retransmission like TCP
            -   checksum sent ahead **Protocol**
-   MAC and ARP
    -   MAC address is data link layer address, 48b
    -   ARP serves to translate IP to MAC
    -   ARP fast cache and ARP broadcast
-   Ping and ICMP
    -   Ping = send ICMP echo
    -   traceroute = if ttl==0 {send-back timeout} else {send-forward
        ttl++}

### Application Layer

-   Web Browser
    -   self info
        -   DHCP broadcast request
        -   return IP, router IP and mask, DNS
    -   router MAC
        -   ARP broadcast request
    -   resolve domain
        -   DNS
        -   return server IP
    -   HTTP connection and transmit
-   Get and Post
    -   as protocol
        -   get = pass argument in url, idempotent
        -   post = pass data in request body, allocate new resource
    -   sender
        -   get = one package with 200 ok
        -   post = one package of header with 100 continue, one package
            of data with 200 ok
    -   format
        -   get: has url length limit, store in ASCI, request body may
            be ignored
        -   get: can be bookmarked and logged and actively cached
-   Session and Cookies
    -   Cookies: saved in client, carried by request to server
        -   distributed and hackable
        -   only ASCII
    -   Session: saved in server, proof of account validation
        -   server send `Set-Cookie(id)` so that client can carry
            session id to avoid repeated validation
-   Errorno in response
    -   100: continue
    -   200: ok
    -   400: bad request
    -   401: unauthorized request
    -   403: forbidden request
    -   404: not found
    -   500: internal server error
    -   503: service unavailable

Operating System & Linux
------------------------

### Memory

-   hardware - kernel - user
    -   copy
        -   `put_user`, `get_user`
    -   mmap
        -   shared with kernel
        -   multi-user conflict by FILE\_LEASE\_SIGNAL
    -   sendfile
        -   Linux 2.1
            -   copy from hardware to kernel
            -   copy from kernel to socket buffer
        -   Linux 2.4
            -   copy from hardware to kernel
            -   send fid to socket buffer
        -   compact, cannot modify data

### Process

#### Process Management

-   State
    -   running
    -   uninterruptible sleep (in IO)
    -   interruptible sleep (wait for event)
    -   zombie (terminated but not reaped)
        -   parent didn't call wait() to recycle the pid
        -   cause possible exaustion
        -   kill parent to solve
    -   stopped
    -   orphan
        -   parent already exit
        -   will be adopted by init process (pid=1)

#### Process Communication

-   IPC
    -   pipe
        -   fd\[0\] read, fd\[1\] write
        -   semi-duplex
        -   only between parent-child process
    -   fifo
        -   queue read request and write request
    -   message queue
        -   independent from process
        -   non-blocking
        -   support selectively receive
    -   semaphore
    -   shared memory
    -   socket
        -   allow cross machine
-   Signal
    -   stored in process local bitmap
    -   process will check bitmap when returning to user mode (due to
        interrupt or syscall), run handler in user context
    -   for interruptible sleeping, `longjmp` and exit syscall with err
    -   `Ctrl+C`
        -   SIGINT
        -   `signal(SIGINT, my_handler)` to register

### Network IO

-   blocking
    -   socket::recv, wait for kernel to buffer a complete message
-   non-blocking
    -   return err instantly
-   multiplexing
    -   select(readset, write, exception)
        -   block until any socket is ready,(wait for fd), then use recv
            (need pool each fd) (two syscall in total)
    -   poll(pollfd\*)
        -   same as select but without readset limitation
    -   epoll `epoll_ctl(epoll_create(), event); epoll_wait`
        -   mmap
        -   level trigger
            -   ceased only when event is processed(state turns to
                ready), can read in steps
        -   edge trigger
            -   need consume at first sight or lose it
            -   only support non-blocking socket, to avoid blocking at
                file end (non-blocking will return EAGAIN)
-   signal-driven
-   async
    -   aio\_read to let kernel signal user

### Filesystem

-   Case Study
    -   Ext2
        -   block index stored in inode
    -   FAT
        -   linked list

Language
--------

### C++

#### Memory

-   static object
    -   initialization order
        -   global::ctor -\> main() -\> f() -\> f::static::ctor -\>
            f::static::dtor -\> global::dtor -\> main::exit()
    -   name-mangling
    -   misc
        -   definition without `public`, `static` modifiers
-   right value reference
-   smart pointer

#### Polymorphism

-   Class layout
-   RTTI

#### Multi-Thread

#### Meta Programming

-   SFINAE (substitution failure is not an error)

#### Standard Library

#### Misc

-   atexit(f)
-   extern "C"
    -   extern
    -   C compiler
        -   no overload, join in global function matching
        -   ``` {.cpp}
            extern "C" {
            int f(int a) {
              return -1;
            }
            }
            template <typename T>
            int f(T t) {
              return (int)t;
            }
            int main(void) {
              cout << f(2.0); // return 2
              return 0;
            }
            ```
-   `dtor{delete p; p = NULL}`
    -   first unnecessary
    -   second will hide underlying logical error

### Java

-   Basic
    -   `native`
    -   `goto`: not used but reserved
    -   primitive type
        -   char = 16-bit
        -   no unsigned integer type
        -   String: support switch case semantics
        -   long: don't support switch case
    -   wrapping class
        -   Constant and Dynamic instance
            -   created through literal
            -   compare with literal is reduced to value
            -   compare with dynamic object is reduced to address
        -   `Integer`
            -   -128 \~ 127 is constant in heap ( if created through
                literal )
        -   `String`
            -   `intern()`
            -   `StringBuffer` is thread-safe
        -   final content
            -   easy to cache hash value
            -   possible to leverate constant pool
            -   thread safety
    -   generic type
        -   `Generic` behaves like `Generic<Object>` with uncheck
            warning
        -   `Generic<?>` container can return `Object`, can't set, even
            with restraint
        -   generic type casting
            -   泛型擦除, cast to `Object`
            -   cast to restricted `Wrapper<T extends Base>`
        -   array with generic type
            -   `Wrapper<?>[] arr = new Wrap<?>[]`
    -   type inference
        -   `ok ? x : y` will appear maximum type, favor int over
            Integer
            -   will automatically unbox both operators
    -   exception
        -   throw - try - catch - finally
            -   finally can override return value, skipped when `exit`
        -   try - with - resource
            -   `AutoCloseable`
            -   `try ( resource = new Resource() ) { }`
        -   ascending chained catch
        -   static exception declaration
        -   `Exception`
            -   can't be generic class
    -   closure
    -   Reference (4 types)
-   Class
    -   Type
        -   Abstract Class
        -   Interface
            -   default method
    -   Inheritance
        -   Override
        -   Overload
    -   Object
        -   `clone()`: shallow copy
        -   `equals()`: type
        -   `toString()`: name + @ + hash
-   Design Pattern
    -   Single Instance
    -   Factory and abstract factory
    -   ClassLoader
-   Standard Data Structure
    -   `fail-fast`: throw `ConcurrentModificationException` on
        concurrent access
        -   `modCount`
    -   `copy-on-write`: lock when applying modification
        -   `CopyOnWriteArrayList`
    -   Iterable and Iterator
        -   `iterator()`
        -   `remove()`: delete last element returned by `next()`
    -   HashMap
        -   Collision
    -   ArrayList
    -   ThreadSafe
-   Parallel Programming
    -   Thread
        -   `run()`, `start()`, `yield()`
    -   Lock
        -   `Object`: `wait()`, `notify()`
    -   Handler and Message Queue
    -   ThreadPool
        -   increasing strategy and denial strategy
    -   juc
    -   Atomic
        -   `AtomicLong` versus `LongAdder`
            -   `Cell`
    -   `AbstractQueuedSynchronizer` (aqs)
-   Network
    -   BIO and NIO and AIO and Socket
        -   netty
            -   1 channelhandler负责请求就绪时的io响应。
                    2 bytebuf支持零拷贝，通过逻辑buff合并实际buff。
                    3 eventloop线程组负责实现线程池，任务队列里就是io请求任务，类似线程池调度执行。
                    4 acceptor接收线程负责接收tcp请求，并且注册任务到队列里。
-   RTTI and Reflection
    -   RTTI
        -   no generic information
        -   `Class.forName(_name)`
    -   serialize
        -   `Serializable`
            -   parent must be Serializable or have default ctor
        -   `transient`
            -   not serialized (use default value like null)
-   JVM
    -   Java -(`JDK`)-\> ByteCode -(`JVM`)-\> MachineCode
    -   ClassLoader
    -   g1 collector
    -   garbage collection
        -   `finalize()`
        -   `System.gc()`
        -   ways to be old
-   Spring
-   Project
    -   `chm`: microsoft, html

Database
--------

Parallel Programming
--------------------

### Paradigm

-   Process
    -   independent resource
    -   schedule by OS
-   Thread
    -   parallel logical flow
    -   schedule by OS, IO blocking and timeslice blocking
    -   own stack and regisster
-   Coroutine
    -   Motivation
        -   block to wait for logical event
        -   or asynchronous callback which require additional state
            -   state in closure
    -   control flow is consistent with logical flow

Systems
-------

Algorithm
---------

### BigData and Online Algorithm

-   Partition Data
    -   natural order
        -   need lut
    -   hash
        -   balancing
    -   logical order
        -   locality but also need lut
-   Repetition
    -   HashSet with partitioned hash
    -   BitSet
    -   Bloom Filter
    -   Trie
-   Top-K
    -   numeric
        -   Heap
    -   occurance
        -   visit by hash then top-k-value
        -   N Hash Table, Freq = min(value from all tables) \>= accurate
            Freq
        -   Trie

Interview Record
----------------

### Bytedance

#### 19-01 Backend Develop Intern at EE

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

### Tencent

#### 19-2 Keen Security Lab

Job Experience
--------------

### Company Overview

#### Hulu

-   base at Beijing

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
