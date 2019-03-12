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

`send` return small integer

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
-   allocator
    -   buddy algorithm
    -   slab
-   memory partition
-   shared memory
    -   implementation
-   program
    -   bss segment: uninitialized, reset before program starts
    -   data segment: initialized data
    -   code / text segment
    -   heap + stack

### Process

#### Process Management

-   State
    -   PCB: `task_struct`
    -   running
    -   uninterruptible sleep (in IO)
    -   interruptible sleep (wait for event)
    -   zombie (terminated but not reaped)
        -   parent didn't call wait() to recycle the pid
        -   cause possible PCB exaustion
        -   solution
            -   kill parent to convert to orphan,
                `signal(SIGCHLD, SIG_IGN)` to ignore child process
            -   set parent `SIGCHLD` handler to wait pid
    -   orphan
        -   parent already exit
        -   will be adopted by init process (pid=1)
    -   stopped
-   process and thread (linux)
    -   `fork`
        -   `copy-on-write`
        -   copy `task_struct`
        -   copy kernel stack
        -   init PCB and copy shared information to child
        -   counter = parent.counter / 2 (timeslice)
        -   enqueue and return pid (self is 0, use `getpid` to get
            actual pid)
    -   `vfork`
        -   share parent address space
            -   child process will modify parent data
        -   parent will wait for child `exit(1)`, or loop at `vfork`
    -   `clone`: thread-like
        -   customized copy, namespace, brother process
        -   use closure parameter
        -   new kernel stack
    -   `exec`: overwrite with new program
-   Process Scheduling

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
    -   `write` process
    -   inode and block
    -   file recovery
    -   soft link and hard link
-   Case Study
    -   Ext2
        -   block index stored in inode
    -   FAT
        -   linked list