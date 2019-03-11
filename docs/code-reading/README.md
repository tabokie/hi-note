-   [Code Reading](#code-reading)
    -   [Reading List](#reading-list)
    -   [Hack](#hack)
    -   [Structure](#structure)

Code Reading
============

Reading List
------------

-   Programming Language
    -   [robovm](https://github.com/MobiVM/robovm): Ahead of time
        compiler for JVM bytecode targetting iOS, Mac OSX and Linux
    -   [Idris-dev](https://github.com/idris-lang/Idris-dev): A
        Dependently Typed Functional Programming Language
    -   [TemplatedPL](https://github.com/Cheukyin/TemplatedPL): A Lisp
        Interpreter Written in C++ Template
-   Language Infrastructure
    -   C++
        -   [EASTL](https://github.com/electronicarts/EASTL): EASTL
            stands for Electronic Arts Standard Template Library. It is
            an extensive and robust implementation that has an emphasis
            on high performance.
        -   [EffectiveSan](https://github.com/GJDuck/EffectiveSan):
            Dynamically Typed C/C++
        -   [libcsptr](https://github.com/Snaipe/libcsptr): Smart
            pointers for the (GNU) C programming language
        -   [muduo](https://github.com/chenshuo/muduo): A C++
            non-blocking network library for multi-threaded server in
            Linux
        -   [handy](https://github.com/yedf/handy):
            简洁易用的C++11网络库 / 支持单机千万并发连接
        -	coroutine
        	-	[libgo](https://github.com/yyzybb537/libgo)
    -   Rust
        -   [futures-rs](https://github.com/rust-lang-nursery/futures-rs):
            Zero-cost asynchronous programming in Rust
    -   Go
        -   [go-callvis](https://github.com/TrueFurby/go-callvis):
            Visualize call graph of a Go program using dot format.
        -   [atomic](https://github.com/uber-go/atomic): Wrapper types
            for sync/atomic which enforce atomic access
-   System
    -   [awesome-distributed-systems](https://github.com/theanalyst/awesome-distributed-systems):
        A curated list to learn about distributed systems
    -   [rDSN](https://github.com/Microsoft/rDSN): Robust Distributed
        System Nucleus (rDSN) is an open framework for quickly building
        and managing high performance and robust distributed systems.
    -   [harvey](https://github.com/Harvey-OS/harvey): A distributed
        operating system
    -   [ems](https://github.com/SyntheticSemantics/ems): Extended
        Memory Semantics - Persistent shared object memory and
        parallelism for Node.js and Python
    -   [drqueue](https://github.com/DrQueue/drqueue): DrQueue is the
        Open Source Distributed Render Queue.
    -   Computing
        -   [fluent](https://github.com/fluent-project/fluent): A
            data-driven compute platform
        -   [gleam](https://github.com/chrislusf/gleam): Fast,
            efficient, and scalable distributed map/reduce system, DAG
            execution, in memory or on disk, written in pure Go, runs
            standalone or distributedly.
    -   Storage
        -   [memdb](https://github.com/rain1017/memdb): Distributed
            Transactional In-Memory Database
            (全球首个支持分布式事务的MongoDB)
    -   Middleware and Protocol
        -   [nsq](https://github.com/nsqio/nsq): A realtime distributed
            messaging platform
        -   [dragonboat](https://github.com/lni/dragonboat): A feature
            complete and high performance multi-group Raft library in
            Go.
        -   [specpaxos](https://github.com/UWSysLab/specpaxos):
            Speculative Paxos replication protocol
        -   [disruptor](https://github.com/LMAX-Exchange/disruptor):
            High Performance Inter-Thread Messaging Library
        -   [phxqueue](https://github.com/Tencent/phxqueue): A
            high-availability, high-throughput and highly reliable
            distributed queue based on the Paxos algorithm.
    -   Tasking
        -   [cpp-taskflow](https://github.com/cpp-taskflow/cpp-taskflow):
            Modern C++ Parallel Task Programming Library
        -   [FiberTaskingLib](https://github.com/RichieSams/FiberTaskingLib):
            A library for enabling task-based multi-threading. It allows
            execution of task graphs with arbitrary dependencies.
        -   [dask](https://github.com/dask/dask): Parallel computing
            with task scheduling
-   Computer Graphics
    -   Render
        -   [filament](https://github.com/google/filament): Filament is
            a real-time physically based rendering engine for Android,
            iOS, Windows, Linux, macOS and WASM/WebGL
        -   [seurat](https://github.com/googlevr/seurat): Seurat is a
            scene simplification technology designed to process very
            complex 3D scenes into a representation that renders
            efficiently on mobile 6DoF VR systems.
        -   [redner](https://github.com/BachiLi/redner): A
            differentiable Monte Carlo path tracer
    -   Platform
        -   [Cinder](https://github.com/cinder/Cinder): Cinder is a
            community-developed, free and open source library for
            professional-quality creative coding in C++.
-   Symbolic Computing
    -   [sympy](https://github.com/sympy/sympy): A computer algebra
        system written in pure Python
    -   [coq](https://github.com/coq/coq): Coq is a formal proof
        management system. It provides a formal language to write
        mathematical definitions, executable algorithms and theorems
        together with an environment for semi-interactive development of
        machine-checked proofs.

Hack
----

-   Generic
    -   `infinity >= 0x3f3f3f3f`
        -   inf + inf == inf
        -   inf = memset(0x3f)

Structure
---------
