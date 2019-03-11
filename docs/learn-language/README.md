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
-   for-range
    -   by value
    -   single capture variable

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
-   goroutine
    -   schedule

Rust
----

### Basic

-   variable
    -   name shadowing by `let`
    -   `const`: annotated, macro
    -   statement and expression
-   struct and type
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
    -   new type: tuple wrapper
        -   encapsulation
        -   alias
    -   never type
        -   `!`
        -   `continue` in code block
    -   dynamically sized type (DST)
        -   Rust do not accept variable-length variable like `str`, use pointer and metadata to construct DST like `&str`, `Box<str>`, `&dyn Trait`
        -   `Sized` trait
            -   `fn normal_generic<T: Sized>(t: T)`
            -   `fn dynamic_generic<T: ?Sized>(t: &T)`
    -   function type
        -   closure trait: `Fn(i32) -> i32`
        -   function pointer type: `fn(i32)->i32`
        -   `()` operator: `.map(TupleName)`
-   generic type and polymorphism
    -   orphan rule (coherence)
        -   can only impl if either Trait of Type is local in crate
        -   workaround: new type
            -   `struct Wrapper(Vec<int>)`: no runtime penalty
            -   could use `Deref` trait to inherit inner type's method
    -   blanket implementation
        -   partial trait implementation
    -   no inheritance
        -   trait default implementation
        -   super trait
            -   `trait SubTrait: SuperTrait`: impl of SubTrait must have impl SuperTrait
    -   dispatch
        -   static dispatch: genericenerate code for concrete type, generic template
            -   `Struct<T> where T: Trait`
        -   dynamic dispatch: use trait as object
            -   dynamic type, must use reference
                -   `Vec<Box<dyn Trait>>`
                -   `&dyn Trait`
            -   `object-safe` trait: use of object do not depend on object concrete type, but only behaviour
                -   return type != `Self`
                -   no generic parameter
            -   lifetime inference ??
    -   trait overloading
        -   `TraitName::func(&instance)`
        -   `<Struct as TraitName>::func()`
-   ownership
    -   stackful and on-heap
        -   stackful data will be copied
            -   no difference between shallow copy and deep copy
        -   on-heap data moves
            -   `Copy` and `Drop` can't coexist \#\#\# Quiz
    -   borrow checker and generic lifetime
        -   lifetime on reference
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
        -   lifetime subtyping
            -   `'a： 'b` means a outlives b
        -   lifetime as type bound
            -   `&'a T` only means the reference to `T` outlives `a`
            -   `<'a, T: 'a> &'a T` now means if `T` is a reference, this reference outlives `a`
-   protocol and contract
    -   `Index`: index should be O(1): why String can't be indexed
    -   `Clone`: Deep clone (not Rc)
-   parallel programming
    -   `Do not communicate by sharing memory; instead, share memory by communicating.`
        -   `channel`: single ownership
        -   shared memory: multiple ownerships
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
        -   `RefCell`: runtime ownership
            -   interior mutability: `borrow_mut()`
            -   more flexible runtime mutability: the structure do not own the data itself
        -   `Arc`: atomic reference counted type
            -   implemented `Send`: transferrance across thread
-   unsafe Rust
    -   deref pointer
        -   ignore borrowing rule
        -   can point to invalid memory or null
        -   no resource cleanup
        -   `let p = &mut data as *mut Data; unsafe { *p; }`
    -   call unsafe function
        -   `extern` is unsafe
            -   `no mangle`
    -   access mutable static variable
    -   implement unsafe trait
-   macro
    -   declarative (`macro_rules!`)
    -   procedural
-   ``` {.rust}
    let val = loop {
      if x > 0 { break -1; }
      0
    }
    ```

Pony
----
