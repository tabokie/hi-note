-   [Learn to be Hired](#learn-to-be-hired)
    -   [Resources](#resources)
    -   [Network](#network)
        -   [Transport Layer](#transport-layer)
        -   [Application Layer](#application-layer)
    -   [Operating System & Linux](#operating-system-linux)
        -   [Memory](#memory)
        -   [Process](#process)
            -   [Process Management](#process-management)
            -   [Process Communication and
                Synchronization](#process-communication-and-synchronization)
        -   [Network IO](#network-io)
        -   [Filesystem](#filesystem)
    -   [Language](#language)
        -   [Generic](#generic)
            -   [Scripting Language](#scripting-language)
            -   [Functional Language](#functional-language)
        -   [C++](#c)
            -   [Underneath](#underneath)
            -   [Memory](#memory-1)
            -   [Polymorphism](#polymorphism)
            -   [Multi-Thread](#multi-thread)
            -   [Template](#template)
            -   [Standard Library](#standard-library)
            -   [Misc](#misc)
        -   [Java](#java)
            -   [Basic](#basic)
            -   [Class](#class)
            -   [Design Pattern](#design-pattern)
            -   [Standard Data Structure](#standard-data-structure)
            -   [Parallel Programming](#parallel-programming)
            -   [IO](#io)
            -   [RTTI and Reflection](#rtti-and-reflection)
            -   [JVM](#jvm)
            -   [Industry](#industry)
    -   [Database](#database)
    -   [Programming Practice](#programming-practice)
        -   [Debugging and Optimization](#debugging-and-optimization)
        -   [Parallel Programming](#parallel-programming-1)
            -   [Overview](#overview)
            -   [Paradigm](#paradigm)
            -   [Memory](#memory-2)
            -   [Lock and Model](#lock-and-model)
            -   [Lock Free](#lock-free)
            -   [Mics](#mics)
    -   [System Design](#system-design)
        -   [Design](#design)
        -   [Communication](#communication)
        -   [Storage](#storage)
    -   [Graphics](#graphics)
        -   [Rendering](#rendering)
        -   [Geometry](#geometry)
        -   [High-Level Algorithm](#high-level-algorithm)
    -   [Algorithm](#algorithm)
        -   [Strategy](#strategy)
        -   [Basic Data Structure](#basic-data-structure)
        -   [Basic Algorithm](#basic-algorithm)
        -   [Advanced Algorithm](#advanced-algorithm)
            -   [Dynamic Programming](#dynamic-programming)
        -   [Probablistic and Randomness](#probablistic-and-randomness)
        -   [BigData and Online
            Algorithm](#bigdata-and-online-algorithm)
        -   [Corner Case](#corner-case)
        -   [Bugs](#bugs)
        -   [Optimization](#optimization)
    -   [Interview Record](#interview-record)
        -   [Project Summary](#project-summary)
            -   [Optix Ray Tracer](#optix-ray-tracer)
            -   [Coplus Parallel Library](#coplus-parallel-library)
            -   [sBase Database](#sbase-database)
            -   [SpecTM transactional
                memory](#spectm-transactional-memory)
        -   [Bytedance](#bytedance)
            -   [Backend Develop Intern at
                EE](#backend-develop-intern-at-ee)
        -   [Alibaba](#alibaba)
            -   [Antfin Big Data Platform
                Intern](#antfin-big-data-platform-intern)
        -   [Tencent](#tencent)
            -   [Keen Security Lab](#keen-security-lab)
        -   [Netease](#netease)
            -   [LeiHuo Game Engine Intern](#leihuo-game-engine-intern)
        -   [Pony.ai](#pony.ai)
    -   [Job Experience](#job-experience)
        -   [Company Overview](#company-overview)
            -   [PingCAP](#pingcap)
            -   [Netease](#netease-1)
            -   [Alibaba](#alibaba-1)
            -   [Hulu](#hulu)
            -   [Google](#google)
            -   [Microsoft](#microsoft)
        -   [Build Open-Source
            Experience](#build-open-source-experience)
        -   [Transfer to USA](#transfer-to-usa)

Learn to be Hired
=================

**todos**

-   [ ] Algorithm

Resources
---------

-   [hacking-the-software-engineer-interview](https://puncsky.com/hacking-the-software-engineer-interview/)
-   Book: <Elements of Programming Interviews>
-   [System Design
    Primer](https://github.com/donnemartin/system-design-primer)
-   [Leetcode VIP](http://206.81.6.248:12306/leetcode/algorithm)

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
    -   9
        -   hold active processes until exaustion of pid
        -   IO multuplexing
        -   hold thread and buffer request until data is ready
    -   Application
        -   response on request
        -   (长轮询) on request, server will wait for data ready or
            timeout
        -   (Comet HTTP stream) continously send data to client
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
            -   0RTT connection establishment
            -   improved congestion control
                -   configured at application layer
                -   accurate RTT sample by ascending packet number +
                    stream offset
                    -   retransmission will use new and higher packet
                        number to avoid ambiguity
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
-   virtual memory and page replacement
    -   paging and segment
    -   redis cache
-   program link
    -   dynamic versus static linkage

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
        -   SIGCHILD to analyse cause
    -   stopped
    -   orphan
        -   parent already exit
        -   will be adopted by init process (pid=1)
-   Process Scheduling
-   Linux thread implementation

#### Process Communication and Synchronization

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
        -   implementation
            -   rbtree: no duplicate
            -   ready list: insert by callback handle
-   signal-driven
-   async
    -   aio\_read to let kernel signal user

### Filesystem

-   basic
    -   inode and block
    -   file recovery
    -   soft link and hard link
-   Case Study
    -   Ext2
        -   block index stored in inode
    -   FAT
        -   linked list

Language
--------

### Generic

#### Scripting Language

-   Definition
    -   simplify repeated work
    -   human-oriented
-   example
    -   `Unix shell`

#### Functional Language

### C++

#### Underneath

-   Compilation
    -   elf: binary under Linux
        -   header: 64 byte
            -   type: relocatable(`.o`) or executable
            -   entry point address (for exe)
        -   program header table (for exe)
        -   section header table
        -   vtable stored in rodata (read-only)
    -   gdb debugger
-   ABI: application binary interface
    -   mangle: sourcecode id -\> ABI id
    -   RTTI `typeid` operator will return ABI id (`.name()`)
    -   demangle: cross-vendor C++ ABI
        -   `abi::__cxa_demangle`
-   Runtime
    -   Function Stack
        -   parameter
            -   from right to left
            -   easily fetch first of variable-length parameters

#### Memory

-   layout
    -   stack
    -   heap
    -   free store: abstract concept
    -   global / static store
    -   constant store
-   static object
    -   initialization order
        -   global::ctor -\> main() -\> f() -\> f::static::ctor -\>
            f::static::dtor -\> global::dtor -\> main::exit()
    -   name-mangling
    -   misc
        -   definition without `public`, `static` modifiers
-   move semantics
    -   move constructor and copy constructor
        -   `template emplace_back(T&&)` \> `push_back(item_type&&)`
    -   right reference
    -   forward reference (universal reference): `T&&`
        -   reference collapsing
    -   copy elision (RVO / NRVO) to optimize return by value
-   allocation
    -   `malloc`: heap
        -   `mmap` for large chunk
        -   `brk`: break pointer in Linux
        -   free list by C library
    -   `new`: free store
    -   `new []`: store size of array, memory chunk \>= memory used
    -   `allocator`
        -   `stl`
            -   128 KB
            -   first: `malloc`/`free`
            -   second: built-in memory pool
    -   `delete`
        -   exception
        -   `delete`: free memory chunk, call dtor if needed
            -   `delete (new int[10])`: no leak
        -   `delete []`: free (p-4) chunk
-   smart pointer
    -   `make_shared` \> `shared_ptr`
        -   target and ref info will be stored in one contiguous block,
            cut down alloc times
            -   drawback: weak\_ref will in essence act like strong
                reference, because weak ref info is stored with obj
        -   atomicly manage memory ??
            -   ``` {.cpp}
                p_ = new Obj();
                // exception
                p = shared_ptr(_p);
                ```

        -   raw pointer reuse:
            -   ``` {.cpp}
                p = new Obj();
                p1 = shared_ptr(_p);
                p2 = shared_ptr(_p);
                // repeat dtor if p1 p2 are released
                ```
    -   get smart pointer copy from class (extension of
        raw-pointer-reuse-problem)
        -   inherit from `std::enable_shared_from_this<Type>`
        -   `shared_from_this() -> shared_ptr<..>`
        -   to avoid `return std::shared_ptr(this);`
        -   one example of `CRTP`
    -   thread-safety
        -   ref-count field: atomic
        -   ref-count + pointer: non-thread-safe

#### Polymorphism

-   class implementation
    -   `func(this);`
-   virtual function: dynamic poly
    -   vtable: table of virutal function pointer
        -   RTTI information
        -   offset-to-top
    -   single inheritance: call vptr + offset
        -   increment virtual function: share same base component
    -   mulitiple inheritance: call vptr\_x + offset
        -   primary-base contains all function (base function + other
            function)
        -   non-primary contains adjustor thunk
            -   `this -= offset; call f();`
    -   Class Layout
        -   primary base for multiple inheritance
        -   offset-to-top: `B b = new DerivedFromAB()`, b will point to
            non-primary base, need offset to rewind to actual object
        -   RTTI
        -   (primary\_v\_ptr-\>) Derived::f()
        -   (non-primary\_v\_ptr-\>) Thunk f()
            -   Thunk is offset operator on `this` pointer
    -   call parent function: explicitly, compiler generated address
    -   virtual inheritance
    -   `override` keyword: avoid wrong implementation
-   function / operator overload: static poly
    -   argument-dependent lookup: lookup range includes all related
        type's namespace
        -   super class and member class
-   RTTI
-   typecast
    -   `static_cast`: static resolve type conversion
    -   `reinterpret_cast`: pointer, integer
    -   `dynamic_cast`: pointer and reference
        -   return NULL is incomplete pointer
        -   throw `bad_cast` if incomplete reference
    -   `const_cast`: const and volatile

#### Multi-Thread

-   `async`
-   `thread`
-   `volatile`
    -   public: do not use register to save intermediate value
    -   no reorder between volatile variable: instruction level
    -   motivation: IO device mapped as memory

#### Template

-   generic programming
-   meta programming
-   type system
    -   `decltype`
        -   `decltype(a_int) b = 0;`
        -   `decltype(0) b = 0;`
    -   `type_traits`
    -   `constexpr`
        -   function
-   two-phase name lookup
-   SFINAE (substitution failure is not an error)
-   new standard
    -   varidic template
    -   auto type deduction
-   compilation
    -   template instantiation
    -   Export Template (deprecated): save source file for late
        compilation
-   variadic template `...`
-   CRTP (curiously recurring template pattern)
    -   `class Derived: Base<Derived>` to implement static polymorphism
        -   `Base<T>::func{ cast<T>::func(); }`
        -   `generic_accept<T>(T t)`
        -   reduce runtime penalty, with more template instance codes
    -   widely used in Java too (`Comparable<T>`)

#### Standard Library

-   container
    -   `list`: double-linked list
    -   `vector`: contiguous array
        -   `push_back`
            -   time complexity: O(c)
                -   $log_m(n)$ times reallocate
                -   each reallocate will execute $m^i$ times moving
    -   `map`: rb-tree
    -   `unordered_map`: hash table
    -   `set`: rb-tree
    -   `unordered_set`: hash table
    -   iterator
        -   invalidation

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

#### Basic

-   `native`
-   `goto`: not used but reserved
-   primitive type
    -   char = 16-bit
    -   no unsigned integer type
    -   String: support switch case semantics
    -   long: don't support switch case
-   variable
    -   local variable definition and initialization at the same time
        -   explicit constraint, for local variable is only used after
            initialized
        -   unlike class member, need information at compilation time
-   wrapping class
    -   Constant and Dynamic instance
        -   created through literal
        -   compare with literal is reduced to value
        -   compare with dynamic object is reduced to address
    -   `Integer`
        -   -128 \~ 127 is constant in heap ( if created through literal
            )
    -   `String`
        -   `intern()`
        -   `StringBuffer` is thread-safe
    -   final content
        -   easy to cache hash value
        -   possible to leverate constant pool
        -   thread safety
-   generic type
    -   `Generic` behaves like `Generic<Object>` with uncheck warning
    -   `Generic<?>` container can return `Object`
    -   Producer Extends, Consumer Super
        -   `Container<? extends T>.get() -> T`
        -   `Container<? super T>.set(T)`
    -   generic type casting
        -   泛型擦除: all <T> is treated as empty
        -   cast to restricted `Wrapper<T extends Base>`
    -   array with generic type
        -   `Wrapper<?>[] arr = new Wrap<?>[]`
-   type inference
    -   `ok ? x : y` will appear maximum type, favor int over Integer
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
    -   Strong Reference: never gc
    -   Soft Reference: gc when necessary
        -   `new SoftReference(ref)`
        -   `new SoftReference(ref, new ReferenceQueue())`
            -   soft\_ref will be queued when ref is released
            -   `while( (ref=queue.poll()) != null )`
    -   Weak Reference: gc asap
        -   `new WeakReference`
        -   `System.gc()`
    -   Phantom Reference
        -   `new PhantomReference(ref, new ReferenceQueue<String>())`
        -   denote that object is about to be collected
    -   `WeakHashMap`: key as weak reference, retire k-v when key is
        retired

#### Class

-   Type
    -   Abstract Class
    -   Interface
        -   default method: `default void f()`
        -   `extends` have priority over `implements`
        -   member variable is implicit `static final`
-   Inheritance
    -   Override
    -   Overload
    -   misc
        -   reference super class
            -   Super.super.f()
    -   no name hiding: except static/variable
    -   dynamic binding: except private/static/final name
        -   implementation
-   initialization: see JVM
-   access control
    -   (default) friendly: package
    -   protected: package and child
-   inner class
    -   in-class static inner class
        -   delayed initialization
    -   in-function inner class
        -   no access modifier
        -   final reference
    -   anonymous class
        -   `new Map() { {put(0,"0")} }`
        -   `new Base(a) { Base(int a) { } }`
-   Object
    -   `clone()`: shallow copy
    -   `equals()`: type
    -   `toString()`: name + @ + hash
    -   method\_info
-   Annotation
    -   implementation
-   Java 9 - Modular

#### Design Pattern

-   Creation
    -   Single Instance
        -   双重校验锁
        -   `volatile` to avoid reorder
            -   `addr = alloc(); new (addr)Obj; ref = addr;`
            -   get addr before the resource is initialized
        -   inner class with delayed initialized member
        -   serialization attack
            -   all fields set to transient and override serializable
                interface
        -   reflection attack
            -   normally, `private Singleton()` to avoid explicit
                construct
            -   using reflect and `ctor.setAccessible(true)`, can call
                private ctor
        -   enum class
            -   one initialization proof by JVM
    -   Factory
        -   simple factory
            -   `newProduct(type) -> Product { new ConcreteProduct() }`
        -   factory method: integrated type
            -   `ConcreteFactory:: newProduct() -> Product`
        -   abstract factory: product family
            -   `ConcreteFactory:: newProductA() -> ProductA`
            -   `ConcreteFactory:: newProductB() -> ProductB`
    -   Builder
        -   `buildPart()` and `getResult() -> Product`
    -   prototype
        -   `clone() -> Prototype`
-   Action

#### Standard Data Structure

-   `fail-fast`: throw `ConcurrentModificationException` on concurrent
    access
    -   `modCount`
    -   throw exception when delete using `for-each` even under
        single-thread \#check-again\#
-   `copy-on-write`: lock when applying modification
    -   `CopyOnWriteArrayList`
-   Iterable and Iterator
    -   `iterator()`
    -   `remove()`: delete last element returned by `next()`
-   Set
    -   TreeSet: rb-tree
    -   HashSet
        -   Collision
        -   如何不影响读写下扩容
    -   LinkedHashSet: ordered iterator
-   List
    -   ArrayList: thread-safe?
        -   `empty_array.add(1, new Object())`:
        -   factor = 1.5
    -   Vector: thread-safe
        -   factor = 2.0
    -   LinkedList
    -   Collection.synchronizedList(new ArrayList());
    -   CopyOnWriteArrayList
        -   single writer
        -   modify pointer
-   Queue
    -   LinkedList
    -   PriorityQueue: heap
-   Map
    -   TreeMap: rb-tree
    -   HashMap
        -   linked list to resolve collision
    -   HashTable: thread-safe
        -   can't insert `null` value
    -   ConcurrentHashMap
        -   Segment Lock: manage partial bucket
        -   default concurrency (num of segment) = 16
        -   `count()`:
            -   each try execute two pass without lock
            -   fourth try will lock all
    -   LinkedHashMap
        -   ordered by usage (LRU)
            -   `accessOrder = false`: insert order
            -   `true`: usage order
    -   WeakHashMap
        -   as cache
            -   ConcurrentCache
        -   value is WeakReference
-   ThreadSafe

#### Parallel Programming

-   Thread
    -   `run()`, `start()`, `yield()`
-   Lock
    -   `synchronized`
        -   Object: `monitorenter` & `monitorexit` instruction
            -   `monitor` lock with counter (reentrant lock)
        -   Method: `ACC_SYNCHRONIZED` identifies stored in
            method\_info, then refer to instance `monitor` lock
        -   optimization: refer to JVM Mark Word
    -   `Object`: `wait()`, `notify()`
    -   `ReentrantLock`
    -   偏向锁 轻量锁 重量锁 转化
-   Handler and Message Queue
-   util
    -   Semaphore
    -   CountDownLatch and Cyclic Barrier
    -   Exchanger
-   ThreadPool
    -   increasing strategy and denial strategy
-   juc
-   Atomic
    -   `AtomicLong` versus `LongAdder`
        -   `Cell`: distribute count value
-   `AbstractQueuedSynchronizer` (aqs)
-   Mics
    -   `volatile`
        -   cache visible
        -   reorder
            -   Release for write operation: all instruction before
                can't be reorder after
            -   Acquire for read operation: all instruction after can't
                be reorder before
            -   `hapen-before`
                -   A =\> flag = true
                -   if flag: B
                -   A is guranteed before B
        -   =\> atomic semantics
    -   atomicity
        -   `int a = 1` is atomic
        -   `volatile long a = 1` is atomic
        -   `Integer a = b` is atomic
            -   based on the fact that `Integer a; a=b;` is same as
                `Integer a = b;`
        -   `long a = 1` is not
        -   `Integer a = new Integer(0)` is not

#### IO

-   File
-   Bytes
    -   Decorator: `FilterInputStream` - DataInputStream -
        BufferedInputStream - PushBackInputStream
-   Chars
-   Object
    -   Serialize Stream
        -   ObjectOutputStream.writeObject(obj)
-   Network
    -   Socket
        -   `Socket(String, int)`
            -   `isConencted()`
        -   `ServerSocket(int)`
            -   `accept()`
    -   UDP Datagram
        -   `DatagrameSocket.send(DatagramPacket(byte[], int))`
-   NIO: block-oriented
    -   Buffer
        -   Channel
        -   Buffer: interaction with channel must pass Buffer
            -   capacity
            -   position: len of read
            -   limit: first invalid index
    -   non-blocking IO (for Socket only)
        -   Selector.open()
        -   SocketChannel.open()
        -   channel.register(selector)
        -   selector.select(): block for all channels
    -   mmap
        -   MappedByteBuffer b = f.map(...)
    -   IO Model
        -   BIO: block
        -   NIO: poll
        -   AIO: callback
    -   netty
        -   channelhandler负责请求就绪时的io响应。
        -   bytebuf支持零拷贝，通过逻辑buff合并实际buff。
        -   eventloop线程组负责实现线程池，任务队列里就是io请求任务，类似线程池调度执行。
        -   acceptor接收线程负责接收tcp请求，并且注册任务到队列里。

#### RTTI and Reflection

-   Get Class Information (RTTI)
    -   no generic information
    -   by instance: `obj.getClass()`
    -   by class name (loader): `Class.forName(_name)`
    -   by class literal: `ClassName.class`
-   Judge Instance
    -   `Object.class.isInstance(new Integer(0)) == true`
    -   obj instanceof Class
-   Create Instance
    -   by class information: `_class.newInstance()`
    -   by class ctor:
        `_class.getConstructor(_arg_class).newInstance(_arg)`
-   Method
    -   get
        -   `[] getDeclaredMethods()`: all locally defined methods,
            `[] getMethods()`: including inherited
        -   `getMethod(String, _para_class)`
    -   call
        -   `invoke(Object instance, _arg)`
-   Field
-   Array
    -   `Array.newInstance(String.class, 10)`
    -   `Array.set(arr, 0, "first")`
-   serialize
    -   `Serializable`
        -   parent must be Serializable or have default ctor
    -   `transient`
        -   not serialized (use default value like null)

#### JVM

-   Java -(`JDK`)-\> ByteCode -(`JVM`)-\> MachineCode
-   对象头 (HotSpot JVM)
    -   Mark Word: 2 machine word = 64-bit
        -   3 word for array to store length
        -   lock word
            -   01 - 0 - age - hashCode
            -   01 - 1 (biased lock) - age - epoch - threadId
            -   00 (light lock) - pointer to lock (in-stack)
            -   10 (heavy lock) - pointer to mutex (monitor)
            -   11 for gc
        -   monitor (heavy lock, mutex)
            -   thread maintain list of free monitors
            -   each object has an associated monitor
            -   `ObjectMonitor`
                -   EntryList: blocked thread
                -   WaitSet: waiting thread
                -   Owner: thread
        -   biased lock: to decrease cost of single main thread
            -   CAS on threadId
            -   if failed, hang owner turn to lightweight lock
        -   lightweight lock: to avoid OS context when single threaded
            -   CAS on mark word (store old word to Desplaced Mark Word)
            -   if failed, spin, if failureed again, turn to heavy lock
                -   **adaptive spinning**
                    -   incr on success
            -   unlock CAS on mark word again
        -   lock coarsening
            -   not granularity
            -   but effective scope
        -   lock elimination
            -   context scan
            -   based on escape analysis
    -   Class Pointer
-   ClassLoader
    -   Load Timing
        -   new instance or use static member
        -   reflection
        -   initialize child class
        -   marked as booting class (java Boot)
        -   JDK1.7 dynamic language support
        -   passive reference
            -   `SubClass.super_class_value`
            -   `new Class[10]`
            -   `Class.const_value`: not final, from constant pool
    -   Loader
        -   Bootstrap ClassLoader: `rt.jar` implemented by C++
        -   Extension ClassLoader: extension `.jar` package
        -   App ClassLoader: classpath
        -   parents delegation
            -   pass request to parent ClassLoader
            -   benifit: uniqueness, avoid error for type information
            -   destory parent delegation: bypass parent loader and load
                customized class with AppClassLoader
                -   how?
        -   exception
            -   `ClassNotFoundException`: load by string, but not found
            -   `NoClassDefFound`: explicitly use class but miss
                `.class` file
    -   Loading Procedure
        -   Load: find `.class` and alloc a `Class` object
        -   Link: varificate, prepare (static member), resolution
            (symbol reference)
        -   Initialize: ctor
    -   JIT runtime editor: optimize hotspot bytecode
        -   HotSpot detection
            -   sample: thread stack top item
            -   counter for method
                -   Invocation Counter
                -   BackEdge Counter: loop back
    -   initialization
        -   `clinit`: combination of static operation
            -   forward assignment, no forward access
            -   parent `clinit` is garanteed to first execute
            -   interface has static final but no static block
                -   parent interface `clinit` will be called only
                    variable is referenced
                -   implementation will not call interface's `clinit`:
                    why??
            -   synchronized
        -   order
            -   super class static variable and block
            -   subclass static variable and block
            -   super class member and block
            -   super class ctor
            -   subclass class member and block
            -   subclass ctor
-   garbage collection
    -   Heap
        -   Young Generation
            -   Eden
            -   From Survivor
            -   To Survivor
        -   Old
        -   Permanent
    -   Method Area
        -   Class
            -   Instance are all collected
            -   ClassLoader is collected
            -   Class object isn't referenced
    -   algorithm
        -   mark and collect
        -   copy: copy living object to shadow half
            -   naive: 0.5 utilization
            -   hotspot: (Eden + From) -\> To
                -   proportion of Eden / Survivor = 8
                -   if survivor \> 0.1, borrow Old Generation area
        -   mark and trim (整理): compact
        -   generation collect
            -   Young and Old
            -   Old uses mark algorithm
    -   Collector

        ![collector](./java-gc-collector.jpg)

        -   Serial: used under single-threaded, hault and collect, no
            context switch
        -   ParNew: multi-threaded, hault and collect
        -   Parallel Scavenge: throughput first (user time percent, not
            respond time)
        -   Serial Old: like serial
        -   Parallel Old: parallel scavenge for old generation
        -   CMS(Concurrent Mark Sweep): mark and collect
            -   Procedure
                -   init: mark GC Roots' direct relatives, block
                -   concurrent: GC Roots Tracing, non-block
                -   re-mark: update to latest, block
                -   concurrent collect: non-block
            -   Drawback
                -   CPU sensitive
                -   floater can't be cleaned, need extra space when
                    collecting
                -   fragment
        -   G1(garbage first): region mark-collect, in-region copy,
            predictable pause
            -   generation are logical concept, memory is divided into
                regions.
            -   prediction is done by partial gc. supported by region
                tracing and priority queue
            -   region's remembered set (referenced region) maintained
                when write reference
                -   write barrier to pause write operation
                -   make it feasible to scan only partial region
            -   Procedure
                -   init
                -   concurrent
                -   final mark: increments stored in remembered set log
                -   filted collect: collect by priority
        -   ZGC: faster than G1
            -   region-based
            -   compacting
            -   NUMA-aware
            -   colored pointer
            -   load barrier

    -   Allocate and Collect
        -   Allocate
            -   enter old generation: big object
                -   long String
                -   array
            -   switch to old generation: old object
                -   age = times to switch to survive minor GC
                -   = threshold will enter old generation

                -   threshold = min (default, majority age)
        -   Collect
            -   minor GC
                -   safe minor GC happens when old contiguous space \>=
                    young (all young upgrade)
                -   danguous minor GC check history upgrades (disabled
                    by `HandlePromotionFailure`)
            -   full GC
                -   triggers
                    -   `System.gc()`: advice
                    -   old heap is full
                    -   PromotionFailure when minor GC
                    -   permanent heap is full
                        -   before JDK 1.7, Method area stored in
                            Permanent Heap
                        -   CMS GC will not GC in this case
                    -   with CMS, old heap is not enough for floater
                        when collecting, `ConcurrentModeFailure`
    -   `finalize()`

-   memory
    -   Thread
        -   PC
        -   Stack
        -   Native Method Stack: works for native methods
    -   Global
        -   Heap: see gc
        -   Method Area: Class and static
            -   Runtime Constant Pool
    -   Direct Memory
        -   `DirectByteBuffer`

#### Industry

-   Spring
    -   循环依赖
    -   Bean lifetime
    -   AOP
        -   implementation
    -   IOC
-   Project
    -   `chm`: microsoft, html

Database
--------

Programming Practice
--------------------

### Debugging and Optimization

-   Memory
    -   Failure (`malloc` failed)
        -   OOM
            -   Large Request
                -   Lazy request
                -   File: mmap
            -   Memory Leak
                -   customized: override `new` operator
                -   compilation: cppcheck
                -   runtime: valgrind (linux), `DumpMemoryLeak` in
                    Windows
            -   OOM Killer: kill by priority
            -   GC raised (in Java): 98% timeslice for GC but only
                retrieve 2% memory
        -   Memory Trespassing
-   Cache
    -   shared cacheline, false sharing
    -   prefetch
    -   register optimization
        -   `++i` \> `i++`
    -   memory alignment
    -   leverage physical arrangement, locality
-   Processor and Instruction
    -   compiler supported optimization:
        -   branch prediction: `likely`
        -   `noexcept`
        -   `inline`
    -   branch elimination
        -   switch-case: lut
        -   bit operation
        -   loop unfold
    -   vector instruction
-   GPU

### Parallel Programming

#### Overview

-   Model
    -   race condition
        -   concurrent access
        -   at least one modifier
    -   protect shared varaiable so that no race condition happens
        -   lock
        -   atomic semantic
        -   internal synchronization: infras for synchronization
-   Design
    -   Algorithm
        -   GPU: CUDA
        -   CPU
    -   Architecture
    -   System
        -   Storage
        -   Computing
        -   Management (schedule)

#### Paradigm

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
    -   Application
        -   Syntex Parser: `while(true): readToken(); go Parse();`
        -   State Machine
    -   Implementation
        -   Stackless Coroutine
            -   Duff's device: `switch (co_data.flag)`
            -   Tolerant to register and stack mutatiion (happens when
                program recover)
        -   Stackful Coroutine
-   Parallel and Concurrent
    -   parallel: multi-task at the same time
    -   concurrent: in the same time interval, handle multi-task
    -   e.g. GPU is parallel not concurrent, single-thread processor is
        concurrent not parallel

#### Memory

-   CAS and ABA
    -   version number

#### Lock and Model

-   Lock
    -   SpinLock: `while(!lock) {}`
        -   TAS (test and set): one CAS
        -   TTAS (spin on read): read + CAS
        -   TTAS with backoff: backoff when CAS failed
        -   drawback
            -   spinning
                -   noop instruction
            -   shared cacheline
                -   avoid collision in time: backoff and cas elimination
                -   avoid collision in space: queue
                    -   spin on next waiter's flag
                    -   spin on flag w.r.t. sequence number
                    -   TicketSpinLock
                    -   MCSSpingLock
            -   clock interrupt (timeslice blocking) when locked
                -   turn off interrupt
    -   Mutex: add thread suspension
        -   drawback:
            -   need OS context
                -   CAS first
    -   ReadWriteLock
        -   rlock: lock(r); r++; if (r == 1) lock(w); // only one locks
            wlock: lock(w);
        -   SequenceLock: resolve write exhaustion
            -   rlock: if (seq is even) read; if (seq != old seq) redo;
                wlock: lock(mu); seq ++;
    -   Lock implementation
        -   CAS
    -   appendix: [Lock C Implementations](./lock-c-impl.txt)
-   Condition Variable: signal to waiters
-   DeadLock
-   Procuder - Customer
-   Performance

#### Lock Free

#### Mics

-   Concurrent Timer

System Design
-------------

### Design

-   Theory
    -   CAP
    -   ACID and BASE
-   Overview
    -   tradeoff
    -   showoff
    -   procedure (from
        `cracking-the-system-design-interview-designing-pinterest-or-instagram-as-an-example`)
        -   requirement and specs
            -   function
            -   scaling
        -   high-level design
        -   individual components and interaction
        -   estimation
-   Components
    -   Load Balancer
    -   Reverse Proxy (contrast to forward-proxy on client,
        reverse-proxy is server-end dispatcher)
    -   Web Server
        -   scaling (stateless)
            -   rps (request-per-second) and bandwidth
            -   vertical scaling (scaling-up)
            -   horizontal scaling (scaling-out)
        -   API
            -   MVC and MVVC
    -   App Service
        -   Service Locater
            -   `Zookeeper`: CP system
            -   `Dynamo`
        -   Micro Service
    -   Database
-   Scalability
    -   methods
        -   horizontal duplication
        -   functional decomposition
        -   horizontal partitioning
-   High Concurrency
    -   asynchronous queue

### Communication

-   Protocol
    -   UDP / TCP
    -   HTTP
        -   REST API
    -   RPC

### Storage

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

Graphics
--------

### Rendering

-   Rasterization Pipeline
    -   forward shading: vertex + fragment shading
        -   forward+ shading: vertex -\> tiled Z-buffer -\> light
            clipping + fragment shading
        -   complexity = Geometry \* Light
    -   deferred shading: vertex -\> G-buffer (depth, normal, albedo)
        -\> shading
        -   complexity = Geometry + Light
            -   clipping (could be done with Z-pre-pass in Forward
                Rendering)
            -   dimension mapping
        -   tile-based deferred shading: vertex -\> tiled G-buffer -\>
            light clipping -\> shading
        -   deferred lighting: use lightweight G-buffer to calculate
            light buffer, then forward pass
            -   allow complex material (in forward pass)
        -   features
            -   memory-inefficient
            -   not support alpha
            -   not support hardware AA
    -   inferred lighting
-   Sample and Anti-Aliasing
    -   SSAA: target scaling
    -   MSAA: multiple sample at fragment shading (hardware AA)
        -   color = sample-hit-rate \* vertex color
    -   FXAA: post-process, vision tech
    -   MFAA: inter-frame
    -   TAA: temporal super sampling
        -   projection matrix jittering
    -   DLSS: RNN
-   Physically-based Render
    -   Geometry Intersection
        -   GPU optimization
    -   Monte-Carlo
        -   importance sampling
            -   light sampling
            -   BSDF sampling
            -   multiple importance sampling
        -   bidirectional ray tracing
-   Coordination
    -   two sets of U-V coordinates
    -   tangent / normal space
        -   tangent space rather than model space
        -   texture reuse
        -   compact normal texture (z \> 0)

### Geometry

-   Triangle
-   Collision Detection
    -   8-ary tree
-   Player Clipping

### High-Level Algorithm

-   Probablistic: see Algorithm
-   Generative ALgorithm
    -   NPC AI
    -   Maze Generate
    -   Path-Finding

Algorithm
---------

### Strategy

-   practice
    -   with speed and amount
    -   whiteboard: plan-ahead, testcase design
    -   verbal: abstracting idea
    -   mock interview

### Basic Data Structure

-   Linked List
    -   intersection
    -   cycle
        -   marker: use data field or pointer field
        -   slow pointer and fast pointer
        -   stack / recursive
-   Hash Table
    -   [collision](http://www.ruanyifeng.com/blog/2018/09/hash-collision-and-birthday-attack.html)

### Basic Algorithm

-   Permutation and Subset O(2\^n)
    -   recursive DFS

    ``` {.python}
    def subset1(self,subset,res,index,nums): 
        if index == len(nums):
            res.append(subset[:]) 
            return 
        subset.append(nums[index]) 
        self.subset1(subset,res,index+1,nums) # 这种情况就是用第idx个数字
        subset.pop(-1) 
        self.subset1(subset,res,index+1,nums) # 这种情况就是不用idx
    ```

    -   divide and conquer

    ``` {.python}
    def subset2(self,subset,res,index,nums): 
        res.append(subset[:]) 
        for i in range(index,len(nums)): 
            subset.append(nums) 
            self.subset2(subset,res,i+1,nums) 
            subset.pop(-1)
    ```

    -   iterative BFS

    ``` {.python}
    def subset3(self,res,nums): 
        stack = [] 
        stack.append([]) 
        while stack: 
            temp = stack.pop()[:]
            res.append(temp) 
            for i in range(len(nums)): 
                if not temp or temp[-1] < nums: 
                    #相当于没有用temp[-1]到 第i-1个数字
                    subset = temp[:] 
                    temp.append(nums) 
                    stack.append(subset) 
        return res 
    ```

-   QuickSort and Partition O(nlogn)
    -   K-th Element in an Array
-   MergeSort and Merge
    -   K-merge
    -   interval-merge
-   Dual Pointer
    -   merge-sort parallel pointer
    -   quick-sort opposite pointer
    -   scan-line 扫描线 p-p
    -   单调栈 p-p
    -   linked-list intersection !!
-   Divide-and-Conquer and Traverse
    -   divide-and-conquer: f = \|\| f(a) + f(b)
    -   traverse: g(res), f(res), res
-   Binary Search
-   BFS
    -   basic form

    ``` {.python}
    def BFS(self, graph, start)
        ans = []
        q = []
        visited = {}

        q.append(start)
        visited[start] = True  

        while q:
            temp = q.pop(0)
            ans.append(temp)
            for n in temp.neighbors:
                if n not in visited:
                    q.append(n)
                    visited[n] = True
        return ans
    ```

    -   level-by-level

    ``` {.python}
    def BFS(self, graph, start)
        ans = []
        q = []
        visited = {}

        q.append(start)
        visited[start] = True  

        while q:
            size = len(q)
            for i in range(size):
                temp = q.pop(0)
                ans.append(temp)
                for n in temp.neighbors:
                    if n not in visited:
                        q.append(n)
                        visited[n] = True
        return ans
    ```

    -   topological sort

    ``` {.python}
    def topSortBFS(self, graph):
        indegree = {}
        ans = []
        for g in graph:
            for n in g.neighbors:
                if n not in indegree:
                    indegree[n] = 1
                else:
                    indegree[n] += 1

        q = []
        for g in graph:
            if g not in indegree:
                q.append(g)

        while q:
            temp = q.pop(0)
            ans.append(temp)
            for n in temp.neighbors:
                indegree[n] -= 1
                if indegree[n] == 0:
                    q.append(n)
        return ans
    ```

### Advanced Algorithm

#### Dynamic Programming

-   Overview
    -   Dynamic Programming = State + State-Transition
    -   Dimension = discrete amount
    -   Augmented Dimension = dual pivot / set
    -   State Transition = Divide-and-conquer / Topology
-   Property
    -   `min`, `max`
    -   `set`

### Probablistic and Randomness

-   Shuffle
    -   random pick from remainder
    -   (online) switch new-comer with insider
    -   (online pick-k) keep first K, for i \> K:
        `x=random(1,i); if x < K: arr[x] = new`
-   Sample
    -   starting point: integer uniform generator
        -   xn+1 = (a \* xn + b) % m

        <!-- -->

            linear congruential method: 线性同余法
            complete cycle (output 0..m before repeat):
            1. b,m inter-prime
            2. m prime factors' product % (a-1) == 0
            3. if M%4==0 then (a-1)%4==0
            4. a,b,x0 < m
            5. a,b > 0

    -   Uniform Sample
        -   rejection sampling
            -   uniform sample in domain area
            -   repeat until accepted
        -   inverse sampling
            -   intersect Cumulative Distribution Function
            -   inverse function
        -   e.g. Circle
    -   scaling map
        -   composing
        -   module
        -   rejection

        <!-- -->

            Rand7:
            while x > 21:
                x = 5 * (Rand5 - 1) + Rand5;
            x % 7 + 1

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

### Corner Case

-   `uint32_t`
-   decimal carry

### Bugs

-   grammar
    -   `>>`
    -   `a + (b) ? x : y`
-   structure
    -   for linked-list, use branch
-   memory
    -   iterating while modifying
    -   modifying while referencing
        -   `back = v.back(); v.push_back(0);`

### Optimization

-   `unordered_map`
-   modular space

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
-   isolation
-   durability

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
