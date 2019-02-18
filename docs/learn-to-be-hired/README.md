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
        -   [Java](#java)
    -   [Database](#database)
    -   [Parallel Programming](#parallel-programming)
    -   [System](#system)
    -   [Algorithm](#algorithm)
        -   [BigData and Online
            Algorithm](#bigdata-and-online-algorithm)
    -   [Interview Record](#interview-record)
        -   [Bytedance](#bytedance)
            -   [19-01 Backend Develop Intern at
                EE](#backend-develop-intern-at-ee)
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

<!-- -->

    1.  DHCP 配置主机信息 假设主机最开始没有 IP
        地址以及其它信息，那么就需要先使用 DHCP 来获取。

    主机生成一个 DHCP 请求报文，并将这个报文放入具有目的端口 67 和源端口 68
    的 UDP 报文段中。

    该报文段则被放入在一个具有广播 IP 目的地址(255.255.255.255) 和源 IP
    地址（0.0.0.0）的 IP 数据报中。

    该数据报则被放置在 MAC 帧中，该帧具有目的地址
    FF:FF:FF:FF:FF:FF，将广播到与交换机连接的所有设备。

    连接在交换机的 DHCP 服务器收到广播帧之后，不断地向上分解得到 IP
    数据报、UDP 报文段、DHCP 请求报文，之后生成 DHCP ACK
    报文，该报文包含以下信息：IP 地址、DNS 服务器的 IP
    地址、默认网关路由器的 IP 地址和子网掩码。该报文被放入 UDP 报文段中，UDP
    报文段有被放入 IP 数据报中，最后放入 MAC 帧中。

    该帧的目的地址是请求主机的 MAC
    地址，因为交换机具有自学习能力，之前主机发送了广播帧之后就记录了 MAC
    地址到其转发接口的交换表项，因此现在交换机就可以直接知道应该向哪个接口发送该帧。

    主机收到该帧后，不断分解得到 DHCP 报文。之后就配置它的 IP
    地址、子网掩码和 DNS 服务器的 IP 地址，并在其 IP 转发表中安装默认网关。

    2.  ARP 解析 MAC 地址 主机通过浏览器生成一个 TCP 套接字，套接字向 HTTP
        服务器发送 HTTP 请求。为了生成该套接字，主机需要知道网站的域名对应的
        IP 地址。

    主机生成一个 DNS 查询报文，该报文具有 53 号端口，因为 DNS
    服务器的端口号是 53。

    该 DNS 查询报文被放入目的地址为 DNS 服务器 IP 地址的 IP 数据报中。

    该 IP 数据报被放入一个以太网帧中，该帧将发送到网关路由器。

    DHCP 过程只知道网关路由器的 IP 地址，为了获取网关路由器的 MAC
    地址，需要使用 ARP 协议。

    主机生成一个包含目的地址为网关路由器 IP 地址的 ARP 查询报文，将该 ARP
    查询报文放入一个具有广播目的地址（FF:FF:FF:FF:FF:FF）的以太网帧中，并向交换机发送该以太网帧，交换机将该帧转发给所有的连接设备，包括网关路由器。

    网关路由器接收到该帧后，不断向上分解得到 ARP 报文，发现其中的 IP
    地址与其接口的 IP 地址匹配，因此就发送一个 ARP 回答报文，包含了它的 MAC
    地址，发回给主机。

    3.  DNS 解析域名 知道了网关路由器的 MAC 地址之后，就可以继续 DNS
        的解析过程了。

    网关路由器接收到包含 DNS 查询报文的以太网帧后，抽取出 IP
    数据报，并根据转发表决定该 IP 数据报应该转发的路由器。

    因为路由器具有内部网关协议（RIP、OSPF）和外部网关协议（BGP）这两种路由选择协议，因此路由表中已经配置了网关路由器到达
    DNS 服务器的路由表项。

    到达 DNS 服务器之后，DNS 服务器抽取出 DNS 查询报文，并在 DNS
    数据库中查找待解析的域名。

    找到 DNS 记录之后，发送 DNS 回答报文，将该回答报文放入 UDP
    报文段中，然后放入 IP
    数据报中，通过路由器反向转发回网关路由器，并经过以太网交换机到达主机。

    4.  HTTP 请求页面 有了 HTTP 服务器的 IP 地址之后，主机就能够生成 TCP
        套接字，该套接字将用于向 Web 服务器发送 HTTP GET 报文。

    在生成 TCP 套接字之前，必须先与 HTTP
    服务器进行三次握手来建立连接。生成一个具有目的端口 80 的 TCP SYN
    报文段，并向 HTTP 服务器发送该报文段。

    HTTP 服务器收到该报文段之后，生成 TCP SYN ACK 报文段，发回给主机。

    连接建立之后，浏览器生成 HTTP GET 报文，并交付给 HTTP 服务器。

    HTTP 服务器从 TCP 套接字读取 HTTP GET 报文，生成一个 HTTP 响应报文，将
    Web 页面内容放入报文主体中，发回给主机。

    浏览器收到 HTTP 响应报文后，抽取出 Web 页面内容，之后进行渲染，显示 Web
    页面。

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

Language
--------

### C++

### Java

Database
--------

Parallel Programming
--------------------

System
------

Algorithm
---------

### BigData and Online Algorithm

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
