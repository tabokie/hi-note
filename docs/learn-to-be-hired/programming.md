-   [Language](#language)
    -   [C++](#c)
        -   [Underneath](#underneath)
        -   [Memory](#memory)
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
-   [Programming Practice](#programming-practice)
    -   [Debugging and Optimization](#debugging-and-optimization)
    -   [Parallel Programming](#parallel-programming-1)
        -   [Overview](#overview)
        -   [Paradigm](#paradigm)
        -   [Memory](#memory-1)
        -   [Lock and Model](#lock-and-model)
        -   [Lock Free](#lock-free)
        -   [Concurrent Util](#concurrent-util)

Language
========

C++
---

### Compilation, Binary and Assembly

-   Compilation
    -   elf: binary under Linux
        -   header: 64 byte
            -   type: relocatable(`.o`) or executable
            -   entry point address (for exe)
        -   program header table (for exe)
        -   section header table
        -   vtable stored in rodata (read-only)
    -   gdb debugger
        -   `g++ -g` to compile
-   ABI: application binary interface
    -   mangle: sourcecode id -\> ABI id
    -   RTTI `typeid` operator will return ABI id (`.name()`)
    -   demangle: cross-vendor C++ ABI
        -   `abi::__cxa_demangle`
-   implementation
    -   function parameter
        -   from right to left
        -   easily fetch first of variable-length parameters
    -   class function
        -   `function(this, ...)`

### Memory

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
-   reference
    -   left reference: pointer
    -   right reference: un-named variable and pointer
        -   move constructor and copy constructor
        -   forward reference (universal reference): `T&&`
            -   reference collapsing
                -   `template emplace_back(T&&)` \> `push_back(item_type&&)`
    -   copy elision (RVO / NRVO) to optimize return by value
-   allocation
    -   `malloc`: heap
        -   `mmap` for large chunk
        -   `brk`: break pointer in Linux
        -   free list by C library
    -   `new` and `delete`: free store
        -   exception safety
            -   ctor should be `no throw`
            -   `bad_alloc`
            -   `new (std::nothrow) Obj`: surpress exception
            -   `set_new_handler( void (*p)() )`
        -   call one dtor if needed, then free chunk
            -   `delete ( new int[10] )` has no leak
    -   `new []` and `delete []`
        -   store size of array
            -   before data chunk
            -   memory chunk \> data chunk
        -   call every dtor, free real chunk (p-4)
    -   placement new `new (addr) Obj`
        -   no allocation, only ctor
    -   `allocator`
        -   `stl`
            -   128 KB
            -   first: `malloc`/`free`
            -   second: built-in memory pool
        -   `tcmalloc` <TODO>
-   smart pointer
    -   `make_shared`
        -   process
            -   allocate sizeof(Obj + Meta)
            -   placement new Obj
            -   initialize Meta
                -   reference count = 1, weak reference = 0
                -   **copy dtor**
                    -   `p = shared_ptr<Base>( make_shared<Derived>() )`
        -   `make_shared` \> `shared_ptr`
            -   target and ref info will be stored in one contiguous block,
                cut down alloc times
                -   drawback: weak\_ref will in essence act like strong
                    reference, because weak ref info is stored with obj
            -   exception safety
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
    -   other smart pointer
        -   `weak_ptr`
        -   `intrusive_ptr`
        -   `scope_ptr`: no-copy
        -   `unique_ptr`: no-copy, ownership

### Polymorphism

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
    -   virtual inheritance
    -   call parent function: explicitly, compiler generated address
    -   `override` keyword: avoid wrong implementation
-   function / operator overload: no (static poly)
    -   argument-dependent lookup: lookup range includes all related
        type's namespace
        -   super class and member class
-   name hiding versus override (virtual)
    -   only call function w.r.t. to reference type

    <!-- -->

        virtual Base::f(A)
        Derived::f(B)
        Base i.f(B) // base
        Derived i.f(A) // derived

    -   case A: different parameter
    -   case B: non-virtual override

-   RTTI
-   typecast
    -   `static_cast`: static resolve type conversion
    -   `reinterpret_cast`: pointer, integer
    -   `dynamic_cast`: pointer and reference
        -   return NULL is incomplete pointer
        -   throw `bad_cast` if incomplete reference
    -   `const_cast`: const and volatile

### Multi-Thread

-   `async`
-   `thread`
-   `volatile`
    -   public: do not use register to save intermediate value
    -   no reorder between volatile variable: instruction level
    -   motivation: IO device mapped as memory

### Template

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
    -   static polymorphism
        -   reduce runtime penalty, with more template instance codes
    -   basic form:
        -   `<T>::useObject(T a) { a.interface() }`
    -   strict form:
        -   `<T>::InterfaceClass { interface(){ cast<T>::interface(); } }`
            -   non-virtual
            -   name-hidding
        -   `InstanceClass: InterfaceClass<InstanceClass>`
        -   `<T>::useObject(InterfaceClass<T> a) { a.interface() }`
    -   std example
        -   `enable_shared_from_this<Object>`
    -   widely used in Java too (`Comparable<T>`)

### Standard Library

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

### Misc

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
-   `const` in defining funtion
    -   parameter
    -   function
    -   return value

Java
----

### Basic

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

### Class

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

### Design Pattern

-   Principle
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

### Standard Data Structure

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
    -   BlockingQueue: mutex and wait on size
        -   ArrayBlockingQueue
        -   LinkedBlockingQueue
        -   PriorityBlockingQueue
    -   ConcurrentLinkedQueue: CAS
-   Map
    -   TreeMap: rb-tree
    -   HashMap
        -   linked list to resolve collision
        -   `Node[] table`
        -   `Node { Hash, Key, Value, Next }`
    -   HashTable: thread-safe
        -   can't insert `null` value
    -   apache.FastHashMap
        -   copy-on-write
    -   netty.IntObjectHashMap
        -   `int[] keys, Object[] values`
        -   open addressing
    -   ConcurrentHashMap
        -   Segment Lock: manage partial bucket
        -   default concurrency (num of segment) = 16
        -   `count()`:
            -   each try execute two pass without lock
            -   fourth try will lock all
    -   ConcurrentSkipListMap
    -   LinkedHashMap
        -   ordered by usage (LRU)
            -   `accessOrder = false`: insert order
            -   `true`: usage order
    -   WeakHashMap
        -   as cache
            -   ConcurrentCache
        -   value is WeakReference
-   ThreadSafe

### Parallel Programming

-   Thread
    -   Runnable: `void run()`, Callable<V>
    -   outer manip
        -   `run()`, `start()`
        -   `interrupt()`
            -   terminate if thread is in blocking / waiting state, throws `InterruptedException`
    -   inner manip
        -   `this.interrupted() -> boolean`
        -   `Thread.sleep(mili)`
        -   `Thread.yield()`
    -   state
        -   new
        -   runnable
        -   blocking: wait for mutex
        -   waiting: `Object.wait()`, `join()`, `LockSupport.park()`->`LockSupport.unpart(Thread)`
        -   timed waiting: `sleep`, `wait(mili)`, `join(mili)`, `parkUntil`
        -   terminated
    -   ThreadLocal
        -   internal: `ThreadLocalMap m = getMap(Thread.getCurrentThread())`
        -   `new ThreadLocal<Object>() { public Object initialValue() }`
    -   ThreadPool
        -   ThreadPoolExecutor / Executor -> ExecutorService
            -  `Executors` have no configuration port, TaskQueue::Size = INT_MAX, will cause OOM
                -   `FixedThreadPool`, `SingleThreadExecutor`, `CachedThreadPool`, `ScheduledThreadPool`(timer)
            -   `ThreadPoolExecutor(corePoolSize, maxPoolSize, workQueue, keepAliveTime)`
                -   increasing policy
                    -   if not reach corePoolSize: add Thread
                    -   if workQueue isn't full: add to workQueue
                    -   if not reach maxPoolSize: add Thread
                    -   if more than corePoolSize and one Thread exceeds keepAliveTime: abandon and shrink
                -   reject policy: `RejectedExecutionHandler`
                    -   if reach maxPoolSize and workQueue full, call rejectedHandle
                    -   `AbortPolicy` throws `RejectedExecutionException`
                    -   `CallerRunsPolicy` run in current thread if executor is alive (coroutine)
                    -   `DiscardOldestPolicy`
                    -   `DiscardPolicy`
            -   shutdown
                -   `shutdown()`: wait for execution
                -   `shutdownNow()`: interrupte all
        -   Pool:: `execute`, `submit -> FutureTask`
-   Synchronization
    -   `Object.wait()`, `Object.notify()`
    -   `lock.newCondition()` -> Condition -> `await()`, `signal()`
    -   Lock
        -   `synchronized`
            -   Object: `monitorenter` & `monitorexit` instruction
                -   `monitor` lock with counter (reentrant lock)
            -   Method: `ACC_SYNCHRONIZED` identifies stored in
                method\_info, then refer to instance `monitor` lock
            -   optimization: refer to JVM Mark Word
    -   `ReentrantLock`
-   Handler and Message Queue
-   `AbstractQueuedSynchronizer` (aqs): lock manager
    -   implementation
        -   fifo by doubly-linked list
        -   `volatile int state`
        -   default function
            -   `getState`, `setState`
            -   `boolean compareAndSetState(int expect, int update)`
        -   interface function
            -   `tryAcquire(int)`, release 
            -   `tryAcquireShared(int)`, release 
    -   Semaphore
    -   CountDownLatch
    -   Cyclic Barrier
-   Exchanger
-   juc
-   Atomic
    -   `AtomicLong` versus `LongAdder`
        -   `Cell`: distribute count value
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

### IO

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

### RTTI and Reflection

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

### JVM

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
        -   mark and sweep
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
        -   CMS(Concurrent Mark Sweep): mark and sweep
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
        -   G1(garbage first): region mark-sweep, in-region copy,
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

Programming Practice
====================

Debugging and Optimization
--------------------------

-   Memory
    -   Failure
        -   OOM (`malloc` failed)
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
            -   `valgrind`
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

Parallel Programming
--------------------

### Overview

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
        -   control flow is consistent with logical flow
            -   block to wait for logical event
            -   or asynchronous callback which require additional state
                -   state in closure
        -   lightweight switch: frequency and dataload and userspace 
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

### Memory

-   CAS and ABA
    -   version number

### Lock and Model

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
        -   CAS + OS
            -   `Futex`: fast userspace mutex
                -   first CAS
                -   then wait by OS
    -   appendix: [Lock C Implementations](./lock-c-impl.txt)
-   Condition Variable
    -   mutex: avoid signal loss
        -   `wait: lock, enqueue, unlock`
        -   `notify` must come with lock
    -   status: avoid spurious(虚假) wakeup
    -   惊群效应
        -   `accept` before linux 2.6
        -   `epoll`
        -   `thread.wait()`
            -   `notify_one`
-   DeadLock
-   Procuder - Customer
-   Performance

### Concurrent Util

-   Concurrent Timer
-   Data Structure
    -   lock-free hash table
        -   lock-free by linked table
        -   leveled hash table
    -   lock-free SPSC circular queue
        -   lamport fifo
        -   linux kfifo
            -   two-power size to avoid modular
