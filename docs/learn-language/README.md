-   [Learn Language](#learn-language)
    -   [C++](#c)
        -   [Parallel Programming](#parallel-programming)
    -   [Go](#go)
        -   [Basic Grammar](#basic-grammar)
        -   [Garbage Collection](#garbage-collection)
        -   [Parallel Programming](#parallel-programming-1)
    -   [Rust](#rust)

Learn Language
==============

C++
---

### Parallel Programming

-   Memory Order
    -   consideration (guard against)
        -   Intel TSO
            -   w-r constraint
                -   clflush
            -   r-r optimization in x86
                -   out-of-order read
        -   Instruction Reorder
    -   method: memory barrier
-   Shared Data
    -   Race Condition
        -   exclusive partial state visit
        -   lock-free
        -   transaction
    -   Lock
        -   built-in
            -   mutex
            -   `lock_guard<mutex>`
        -   dead lock
    -   Atomic
        -   implemented with lock or intrinsic
            -   is\_lock\_free()
        -   atomic\_flag is natural atomic
            -   test\_and\_set
            -   clear
-   std::thread
    -   lifetime
        -   sync
            -   join() or detach() to determine whether synchronize in
                current scope
                -   joinable()
                -   defer join()
                -   detach(): daemon thread, must be joinable to detach
            -   condv, future
        -   detor: call std::terminate()
    -   capture
        -   pass to ctor of thread
            -   direct capture
                -   copy naked pointer
                -   new shadow object for naked reference
                    -   use std::ref(var) to reference
                -   move resource (unique\_ptr)
                    -   thread itself is move-needed resource
                        -   `vec.push_back(a_thread)`
                        -   `std::for_each(v.begin(), v.end(), mem_fn(&thread::join))`
            -   class capture
                -   ctor for non-static function of class instance
                    -   std::thread(&ClassName::Function,
                        &ClassInstance, Parameters...)
        -   closure capture
            -   array capture
                -   `[x = arr[i]]`: capture value
                -   `[&] { x = arr[i]; }`: capture pointer
    -   thread info
        -   a\_thread.get\_id()
        -   std::this\_thread::get\_id()

Go
--

### Basic Grammar

-   dynamic type
    -   `after, ok := before.(int)`
    -   `switch before.(type) { case int }`
    -   `switch after := before.(type) { case int: return after }`

### Garbage Collection

-   mark and sweep
    -   go sweep();
    -   Three color algorithm
    -   write barrier
-   escape analysis
    -   -gcflags=-m

### Parallel Programming

-   Channel
    -   closing principle: close at single sender
    -   safe close using panic handle
    -   safe close using sync
    -   safe close by symmetry or moderator
    -   safe receive
-   sync
-   context

Rust
----
