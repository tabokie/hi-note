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
    -   [Misc](#misc)

Network
-------

### Transport Layer

**HTTP connection**

-   HTTP/0.9 short connection
    -   client-server model
    -   close on each reqeust, request = { DNS -\> three-step -\> transport -\> four-step
        }
    -   only `GET` request, used to transmit HTML text (ASCII protocol)
-   HTTP/1.0
    -   ASCII header + anytype content
    -   durable connection proposal
        -   Connection Field = Keep-Alive / Close
        -   timeout = 5, max = 100
        -   Server
            -   hold active processes until exaustion of pid
            -   IO multuplexing
            -   hold thread and buffer request until data is ready
        -   Application
            -   response on request
            -   (长轮询) on request, server will wait for data ready or
                timeout
            -   (Comet HTTP stream) continously send data to client
    -   command: `PUT`, `PATCH`, `HEAD`, `OPTIONS`, `DELETE`
-   HTTP/1.1
    -   durable conenction put into use
    -   pipelined connection
        -   default Keep-Alive
        -   Head-of-Line-Blocking(HOLB) problem
            -   multiple connection single queue
    -   cache control
-   HTTP/2
    -   multiplexing
        -   stem from Google SPDY
        -   streamID, allow disorder and priority queue
    -   hpack to compress header
    -   binary frame instead of text
    -   server push
    -   range request: `Chunked transfer-coding`
-   HTTP/3: see `QUIC` in UDP section
-   HTTP Cache <TODO>
-   HTTPs
    -   HTTP + SSL / TLS, port 443, server certificate A (private,
        public) and client random private key B
    -   procedure
        -   server send certificate to client
        -   client validate and encrypt random key to server (use
            A.public)
            -   validate certificate (against middle-man attack)
                -   CA send private(hash(certificate)) as info
                -   client check public(info) == hash(certificate)
        -   server get client key (use A.private)
        -   server send content (use B)
-   HTTP Field
    -   keep-alive
    -   cache-control
        -   no-cache
        -   max-age

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
            -   drawback
                -   use UDP, making NAT device hard to identify connection end point
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

-   memory partition
-   virtual memory
-   memory allocation
    -   allocation algorithm
        -   buddy
        -   slab
    -   shared memory
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
    -   motivation for virtual memory, for address translation, optimizing address translation
    -   page and segment
        -   page is unit, while segment is chunk
        -   logical address -\> segmentation unit -\> linear address -\>
            paging unit -\> physical address
-   program link
    -   dynamic versus static linkage
-   program
    -   bss segment: uninitialized, reset before program starts
    -   data segment: initialized data
    -   code / text segment
    -   heap + stack
-   imported
    -   physical frames is described by `page` structure
    -   (physical) pages are divided into zones: DMA, NORMAL, HIGHMEM(not
        directly mapped to kernel, deprecated in 64-bit)
        -   DMA 16MB, NORMAL 16MB-896MB (taken by kernel)
        -   64-bit do not need because kernel address can fully cover memory
    -   virtual memory allocation
        -   first 3 GB is user space
        -   highest 1 GB is kernel space
            -   -\>896 MB is direct mapped to physical memory
            -   rest is vmapped
    -   allocating physical memory
        -   alloc\_page, and page\_address()
        -   kmalloc (contiguous), and kfree
        -   vmalloc (non-contiguous)
            -   not for hardware device
            -   needs setup page table, overhead
            -   TLB thrashing ???
            -   only for large chunk of memory
    -   buddy system and slab
    -   VMA
        -   stored in mm\_struct
        -   mmap linked list: used for iterate
        -   mm\_rb rb-tree: used for searching

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
-   Creation (linux)
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
            -   use `CLONE_VM = true`: share memory space
            -   use `CLONE_FILES = true`
            -   use `CLONE_PARENT = true`: become brother
            -   `CLONE_THREAD`: thread group
        -   use closure parameter
    -   `exec`: overwrite with new program
    -   thread and process
        -   kernel-thread: use `clone (CLONE_THREAD = true)`
            -   `gettid -> pid_t`
            -   `task_struct* group_leader`
            -   `mm == NULL`
        -   pthread (user-thread)
            -   `pthread_self -> pthread_id`: offset to task\_struct
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
-   interrupt driven

    -   interrupt types: hardware, error, user request
        (software-generated trap)
    -   interrupt vector, atomic execution to avoid lost interrupt
    -   save address of interrupted instruction and transfer control to
        interrupt service
    -   handle by polling or vectored system

    -   hardware interrupt: IO
        -   sync and async
        -   DMA: one interrupt per block
    -   software interrupt: system call
        -   api: Win32, POSIX, Java(for JVM)
        -   parameter: register, pointer, stack
        -   types: process control, file management, device management,
            information, communication
    -   top half and bottom half:
        -   top is quick and can't block (less but critical work,
            hardware related work)
        -   bottom
    -   -- Top Half --
    -   hardware to kernel ask for interrupt handler in interrupt
        context (atomic context)
    -   handler is create as static function needs parameter (interrupt
        number, data pointer)
    -   handler is registered by driver (unregistered when driver
        unload)
        -   register needs interrupt number and handler
    -   during interrupt, the number is masked so that same interrupt
        can't be re-entered
    -   interrupt context
        -   no current pointer (invalid)
        -   own interrupt stack
    -   disable / enable interrupt from interrupt or process context
        (the same as masking the bit vector)

-   Syscall
    -   software interrupt (exception) which signals kernel to execute the
        exception handler
    -   syscall number transferred to kernel by `eax` register, return
        through it too. parameter passed by 5 registers and user space
        pointer
    -   process
        -   save all: store number and register needed to stack
        -   store task\_struct pointer to register
        -   is number valid (\<NV\_syscalls), return -ENOSYS
        -   call handler
    -   system call context
        -   process context, current point to user requester
        -   sleepable
            -   kernel functionality
                -   e.g. page fault when read user data
        -   preemptible
            -   new task can preempt and execute the same system call
            -   system call must be reentrant (by different process in this
                context)
        -   stronger than interrupt handler
            -   ??? syscall is interrupt
            -   software interrupt versus device interrupt ???
            -   during system call, interrupt handler is first running in
                interrupt context, then switch to kernel mode and with
                process context
            -   traditionally interrupt refers to hardware interrupt
    -   from user perspective
        -   libc function
        -   software interrupt by `int 0x80`
        -   system\_call() (look up syscall program)
        -   ret\_from\_sys\_call()

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
-   FCB
    -   contains Basic Info, Control Info, Manipulating Info
    -   new file struct every time call `open`, contains file pointer
        information, which points to inode
    -   share file when call `dup` or `fork`
    -   file descriptor
        -   POSIX require use min fid available
        -   fid is process local
        -   1024 at most maybe, can be changed
-   super block -\> inode table -\> data
-   dentry and inode
    -   dentry: logical structure (name and directory)
    -   inode: physical file structure
-   soft link: new inode with relative directory information
-   hard link: new dentry point to the same inode
    -   modification will apply to the origin inode
    -   inode has reference counter, will erase when counter == 0

### Misc

-   Shell
    -   used in linux experiment
        -   `tar`
            -   `tar -x?f file` to extract file
                -   ? can be empty(tar), z (tar.gz), j(tar.bz2),
                    Z(tar.Z)
            -   `tar -c?f target.tar files...`
            -   `rar e` and `rar a`
            -   `gzip -d` and `gzip`
        -   `>>`, `>`, `tee`: redirect
            -   `>>` is add mode
            -   tee -, tee x.txt
        -   `&`: run background; `|`: pipe, output as next input; `;`:
            all
        -   `uname -r`
        -   `gedit /boot/grub/grub.cfg`: check boot options
        -   `watch -n 1 -d ...`, `more`, `less`, `tail -f -n`,
            `tail -F`(create when deleted), `tailf`(silent if not
            growing)
        -   `ln`
        -   `~`
        -   `!!`: show then redo last command, `[!ac1-3]`, `!-1`: last 1
            command
        -   `chmod` and `chown`
        -   `ps` , `top`, `kill`
        -   `touch`
    -   mentioned in book
        -   `here` document
            -   \<\< !Marker! !Marker!
        -   `exec`: return to parent, not caller
            -   `exec < file`: redirect this process
            -   `exec < /dev/tty`: reset to terminal
            -   `exec 1>out 2>err`
        -   `trap`: set handler
            -   `trap 'rm -f tmp' INT`: or 2 for SIGINT
        -   `insmod`, `rmmod`, `lsmod`
        -   `mount` and `dd` for file system
            -   `dd if=input of=output bs=size count=1`
            -   `mount -t type -o loop from to`
            -   `umount to`
        -   `make install` for kernel
            -   `make mrproper`, `make menuconfig`
            -   kernel image: `linux/arch/i386/boot/bzImage`
            -   kernel configuration: `/boot/config`
-   Booting
    -   process
        -   BIOS
            -   check device and initialize
            -   read disk MBR and execute
        -   MBR: Master Boot Record
            -   bootloader
            -   DPT(disk partition table)
                -   at most 4 with at most one extension in it
            -   Marker: 55AA AA55
        -   image and ramdisk
            -   initrd.img: ram as virtual disk
        -   initialize system
            -   /etc/rc.d/rc.sysinit
    -   kernel programming
        -   only gnu-c, no lib-c
        -   no memory protection
        -   no floating point, floating point traps and enter FP mode in
            user mode
        -   small and fixed-size stack
