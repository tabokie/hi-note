-   [Learn Language](#learn-language)
    -   [C++](#c)
        -   [Parallel Programming](#parallel-programming)
    -   [Golang](#golang)
        -   [Basic Grammar](#basic-grammar)
        -   [Garbage Collection](#garbage-collection)
        -   [Parallel Programming](#parallel-programming-1)
    -   [Rust](#rust)
        -   [Basic](#basic)
    -   [Pony](#pony)

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

Golang
------

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

### Basic

-   variable
    -   name shadowing by `let`
    -   `const`: annotated, macro
    -   statement and expression
-   struct
    -   initialization
        -   `{..instance0}`
        -   `{member}`, `{member: value}`
    -   automatic referencing and dereferencing
        -   `p.func()`
    -   unboxing
        -   exausting `match` and specific `if let`
    -   deref coercion
        -   compiler will use `Deref` trait to attempt type fitting
        -   e.g. `&String` -\> `&str`:
            `add(String, &str){}(a_string, &a_string)`
        -   e.g. `*Box<T> -> *(Box<T>.deref()) -> *(&T)`
        -   `DerefMut` and `Def` (convert a &mut to &)
-   generic type and polymorphism
    -   orphan rule (coherence)
    -   blanket implementation
        -   partial trait implementation
-   ownership
    -   stackful and on-heap
        -   stackful data will be copied
            -   no difference between shallow copy and deep copy
        -   on-heap data moves
            -   `Copy` and `Drop` can't coexist \#\#\# Quiz
    -   borrow checker and generic lifetime
        -   function
            -   `&'a`: input variable lifetime outlives deduced lifetime
            -   output variable has deduced lifetime
            -   generic lifetime must be related to input variable
        -   struct
        -   lifetime elision rules
            -   each reference parameter gets its own lifetime parameter
            -   if there is only one parameter, it's assigned to output
                lifetime
            -   if there is a self reference (`&self`), it's assigned to
                output lifetime
        -   static lifetime
            -   literal strings
-   protocol and contract
    -   `Index`: index should be O(1): why String can't be indexed
    -   `Clone`: Deep clone (not Rc)
-   standard
    -   zero-cost lazy iterator
    -   smart pointer
        -   basic usage: stores resource or act as generic handle or
            enable recursive structure
        -   `Box`: unique owner of heap-allocated resource
            -   `Deref`: see deref coercion
            -   `Drop`: customized resource release
                -   call `std::mem::drop()` to explicit release, cause
                    ownership invalidate too
        -   `Rc`: reference counted immutable
            -   `Rc::clone` and `rc.clone()`: prefer first w.r.t. deep
                clone protocol
-   ``` {.rust}
    let val = loop {
      if x > 0 { break -1; }
      0
    }
    ```

Pony
----
