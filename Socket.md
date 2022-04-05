# Socket💊

Socket 起源于 unix，而 unix 基本哲学之一就是 “一切皆文件” ，都可以用 “open –> write/read –> close” 模式来操作，socket 就是该模式的一个实现。

Socket 即是一种特殊的文件，一些 Socket 函数就是对其进行的操作（读、写、打开、关闭）

Socket 就是应用层与 TCP/IP 协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket 其实就是一个门面模式，它把复杂的 TCP/IP 协议族隐藏在 Socket 接口后面，对用户来说，一组简单的接口就是全部，让 Socket 去组织数据，以符合指定的协议。





## 阻塞与非阻塞

阻塞和非阻塞关注的是程序在等待调用结果时的状态

- 阻塞调用：是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回。
- 非阻塞调用：指在不能立刻得到结果之前，该调用不会阻塞当前线程。



## 同步与异步

同步和异步关注的是消息通信机制。

- 同步：就是在发出一个调用时，在没有得到结果之前，该调用就不返回。但是一旦调用返回，就得到返回值了。换句话说，就是由调用者主动等待这个调用的结果。
- 异步：调用在发出之后，这个调用就直接返回了，没有返回结果。换句话说，当一个异步过程调用发出后，调用者不会立刻得到结果。而是在调用发出后，被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用。





## Socket描述符





## TCP连接过程

1. 服务器必须准备好接受外来的连接。这通过调用 socket、bind 和 listen 函数来完成，称为被动打开（passive open）。

2. 客户通过用 connect 进行主动打开（active open）。这引起客户 TCP 发送一个 SYN 分节（表示同步）

3. 服务器必须确认客户的 SYN，同时自己也得发送一个 SYN 分节，它含有服务器将在同一连接中发送的数据的初始序列号。服务器以单个分节向客户发送 SYN 和对客户 SYN 的 ACK。

4. 客户必须确认服务器的 SYN。



<img src="http://store.secretcamp.cn/uPic/image-20210815223719910202108152237201629038240STUuLlSTUuLl.png" alt="image-20210815223719910" style="zoom:50%;" />

1. 某个应用进程首先调用 close，称这一端执行主动关闭（active close）。这一端的 TCP 发送一个 FIN 分节，表示数据发送完毕。

2. 接收到 FIN 的另一端执行被动关闭（passive close）。这个 FIN 由 TCP 确认。它的接收也作为文件结束符传递给接收方应用进程（放在已排队等候该应用进程接收的任何其他数据之后），因为 FIN 的接收意味着应用进程在相应连接上再也接收不到额外数据。

3. 一段时间后，接收到文件结束符的应用进程将调用 close 关闭它的套接口， TCP 也发送一个 FIN。

4. 接收到这个 FIN 的原发送方 TCP（即执行主动关闭的那一端）对其进行确认

<img src="http://store.secretcamp.cn/uPic/image-20210815223852576202108152238521629038332vlZsjVvlZsjV.png" alt="image-20210815223852576" style="zoom:50%;" />



# Socket接口函数💊

<img src="http://store.secretcamp.cn/uPic/image-20210421211325259202104212113251619010805OaL2IbOaL2Ib.png" alt="image-20210421211325259" style="zoom: 48%;" />



## socket

为了执行网络 I/O，一个进程必须做的第一件事情就是调用 `socket()` 函数，指定期望的通信协议类型。

```c++
#include<sys/socket.h>
int socket (int family, int type, int protocol)
```



## connect

TCP 客户用 connect 函数来建立一个与 TCP 服务器的连接。

如果是 TCP 套接口的话，函数 connect 激发 TCP 的三次握手过程，且仅在连接建立成功或出错时才返回。



## bind

 bind 函数给套接口分配一个本地协议地址，对于网际协议，协议地址是 32 位 IPv4 地址或 128 位 IPv6 地址与 16 位的 TCP 或 UDP 端口号的组合。



## listen

函数 listen 仅被 TCP 服务器调用，它做两件事情

- 当函数 socket 创建一个套接口时，它被假设为一个主动套接口，也就是说，它是一个将调用 connect 发起连接的客户套接口，函数 listen 将未连接的套接口转换成被动套接，指示内核应接受指向此套接口的连接请求。调用函数 `listen` 导致套接口从 CLOSET 状态转换到 LISTEN 状态 

- 函数的第二个参数规定了内核为此套接口排队的最大连接个数。



## accept

accept 由 TCP 服务器调用，从已完成连接队列的队首返回下一个已完成连接。若已完成连接队列为空，则进程睡眠（假定套接口为缺省的阻塞方式）。



## close



# I/O模型💊

一个输入操作通常包括两个阶段：

- 等待数据准备好
- 从内核向进程复制数据

对于一个套接字上的输入操作，第一步通常涉及等待数据从网络中到达。当所等待数据到达时，它被复制到内核中的某个缓冲区。第二步就是把数据从内核缓冲区复制到应用进程缓冲区。

以下是五种 I/O 模型总览：

<img src="http://store.secretcamp.cn/uPic/image-20210816001642072202108160016421629044202ABqOZmABqOZm.png" alt="image-20210816001642072" style="zoom:50%;" />



## 阻塞式I/O

阻塞式 I/O 模型是 “阻塞同步” 的。

应用进程被阻塞，直到数据从内核缓冲区复制到应用进程缓冲区中才返回。

在阻塞的过程中，其它应用进程还可以执行，因此阻塞不意味着整个操作系统都被阻塞。因为其它应用进程还可以执行，所以不消耗 CPU 时间，这种模型的 CPU 利用率会比较高。

下图中，`recvfrom()` 用于接收 Socket 传来的数据，并复制到应用进程的缓冲区 buffer 中。这里把 `recvfrom()` 当成系统调用。

```c
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags, struct sockaddr *src_addr, socklen_t *addrlen);
```



<img src="http://store.secretcamp.cn/uPic/image-20210409101707795202104091017081617934628V3N95FV3N95F.png" alt="image-20210409101707795" style="zoom:50%;" />



## 非阻塞式I/O

非阻塞式 I/O 模型是 “非阻塞同步” 的。

应用进程执行系统调用之后，内核返回一个错误码。应用进程可以继续执行，但是需要不断的执行系统调用来获知 I/O 是否完成，这种方式称为轮询（polling）。

由于 CPU 要处理更多的系统调用，因此这种模型的 CPU 利用率比较低。

<img src="http://store.secretcamp.cn/uPic/image-202104091018083142021040910180816179346881b0n1R1b0n1R202104091018131617934693H584UIH584UI.png" alt="image-20210409101808314" style="zoom:50%;" />



## I/O多路复用

I/O多路复用模型的 select 是阻塞的，读数据阻不阻塞无所谓，因为此时数据一定到达，读过程是同步的。

使用 select 或者 poll 等待数据，并且可以等待多个套接字中的任何一个变为可读。这一过程会被阻塞，当某一个套接字可读时返回，之后再使用 recvfrom 把数据从内核复制到进程中。

> 监听 socket 事件的 epoll、poll、select 是阻塞的，对多个 socket 的操作是非阻塞的



它可以让单个进程具有处理多个 I/O 事件的能力，这又被称为事件驱动 I/O。

如果一个 Web 服务器没有 I/O 复用，那么每一个 Socket 连接都需要创建一个线程去处理。如果同时有几万个连接，那么就需要创建相同数量的线程。相比于多进程和多线程技术，I/O 复用不需要进程线程创建和切换的开销，系统开销更小。

<img src="http://store.secretcamp.cn/uPic/image-20210409102003435202104091020031617934803wtQoCcwtQoCc.png" alt="image-20210409102003435" style="zoom:50%;" />



## 信号驱动I/O

信号驱动 I/O 模型的系统调用是非阻塞的，读过程阻不阻塞无所谓，因为数据一定到达，读过程是同步的。

应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，也就是说等待数据阶段应用进程是非阻塞的。内核在数据到达时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中。

相比于非阻塞式 I/O 的轮询方式，信号驱动 I/O 的 CPU 利用率更高。

<img src="http://store.secretcamp.cn/uPic/image-20210409102141598202104091021411617934901vqDAh6vqDAh6.png" alt="image-20210409102141598" style="zoom:50%;" />



## 异步I/O

异步 I/O 模型是 “非阻塞异步” 的。

应用进程执行 aio_read 系统调用会立即返回，应用进程可以继续执行，不会被阻塞，内核会在所有操作完成之后向应用进程发送信号。

异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 I/O 完成，而信号驱动 I/O 的信号是通知应用进程可以开始 I/O。



<img src="http://store.secretcamp.cn/uPic/image-20210409102225135202104091022251617934945pXI5HLpXI5HL.png" alt="image-20210409102225135" style="zoom:50%;" />



## 五大模型比较

- 同步 I/O：将数据从内核缓冲区复制到应用进程缓冲区的阶段（第二阶段），应用进程会阻塞。
- 异步 I/O：第二阶段应用进程不会阻塞。

同步 I/O 包括阻塞式 I/O、非阻塞式 I/O、I/O 复用和信号驱动 I/O ，它们的主要区别在第一个阶段。

非阻塞式 I/O 、信号驱动 I/O 和异步 I/O 在第一阶段不会阻塞。

<img src="http://store.secretcamp.cn/uPic/image-20210409102404097202104091024041617935044KXcCXuKXcCXu.png" alt="image-20210409102404097" style="zoom:50%;" />





# 零拷贝💊

## 内核复制数据流程

用户进程想要获得各种缓冲区里的数据，都必须依靠系统调用

以阻塞的读操作为例：

1. 发起 read 系统调用，要求其用户空间的缓冲区被填满，用户态切换到内核态
2. 内核执行相关内核进程，检查对应内核缓冲区中是否存在数据
   - 如果有，则将数据拷贝到用户进程，返回用户态
   - 如果没有，将内核进程移到等待队列，将加载数据的任务委托给 DMA 控制器，DMA 控制器会异步的将数据从 I/O 设备中加载到缓冲区，加载完成后会触发 DMA 中断，然后内核将内核进程从等待队列移入就绪队列，将缓冲区内的数据拷贝到用户进程，拷贝完成后唤醒用户进程，返回用户态。

>  这里的阻塞只有针对内核进程，但是用户线程表现上看也是被阻塞了，因为断点和寄存器都被压入堆栈，在内核进程返回前是无法继续执行的。

<img src="http://store.secretcamp.cn/uPic/image-20210816112811400202108161128111629084491wBGXu1wBGXu1.png" alt="image-20210816112811400" style="zoom:26%;" />





## 传统I/O方式

用户程序如果想将数据从硬盘传送到网卡，需要先后进行两次系统调用 read 和 write

- 涉及两次 DMA 复制：从磁盘复制到内核缓冲区、从 socket 缓冲区复制到网卡
- 涉及两次 CPU 复制：从内核缓冲区复制到用户数据缓冲区、从用户数据缓冲区复制到 socket 缓冲区
- 涉及四次用户态与内核态的切换：read 调用、read 返回、write 调用、 write 返回

<img src="http://store.secretcamp.cn/uPic/image-20210816113752872202108161137531629085073TJs2etTJs2et.png" alt="image-20210816113752872" style="zoom:40%;" />



磁盘中的数据是先读取到内核的缓冲区中，然后再从内核的缓冲区复制到用户的缓冲区。

因为用户空间的进程是不能直接操作硬件的，磁盘是基于块存储的硬件设备，它一次操作固定大小的块，而用户请求请求的可能是任意大小的数据块。因此，将数据从磁盘传递到用户空间，由内核负责数据的分解、再组合。

<img src="http://store.secretcamp.cn/uPic/image-20210907113024763202109071130251630985425F50FnpF50Fnp.png" alt="image-20210907113024763" style="zoom: 40%;" />





## mmap实现零拷贝

DMA 加载磁盘数据到内核缓冲区后，将用户数据缓冲区和内核缓冲区映射到同一块物理内存，CPU 直接将数据从内核缓冲区复制到 socket 缓冲区。

避免了内核缓冲区到用户数据缓冲区的复制过程，减少了一次 CPU 复制过程。

<img src="http://store.secretcamp.cn/uPic/image-20210907113215221202109071132151630985535RYpKf5RYpKf5.png" alt="image-20210907113215221" style="zoom:40%;" />



要求：

1. 用户缓冲区与内核缓冲区必须使用相同的页大小对齐。
2. 缓冲区的大小必须是磁盘控制器块大小的倍数（磁盘扇区大小为 512 字节），一次只能操作固定大小的数据块。

<img src="http://store.secretcamp.cn/uPic/image-20210815232539253202108152325391629041139rowqXTrowqXT.png" alt="image-20210815232539253" style="zoom:30%;" />





## sendfile实现零拷贝

当调用 sendfile 时，DMA 将磁盘数据复制到内核缓冲区，然后将内核缓冲区直接拷贝到 socket 缓冲区。

完全绕过了用户数据缓冲区的操作，避免了一次 CPU 复制和两次上下文切换。

<img src="http://store.secretcamp.cn/uPic/image-20210815232933565202108152329331629041373rg80njrg80nj.png" alt="image-20210815232933565" style="zoom:33%;" />





# I/O多路复用💊

I/O 多路复用：多路指的是多个 Sokcet，复用指复用一个线程

单线程同时监测若干个文件描述符是否可以执行 IO 操作的能力，即通过记录跟踪每一个 Socket 的状态来同时管理多个 I/O 流。

<img src="http://store.secretcamp.cn/uPic/image-20210421214443760202104212144431619012683jATdpljATdpl.png" alt="image-20210421214443760" style="zoom:67%;" />

CPU 单核在同一时刻只能做一件事情，一种解决办法是对 CPU 进行时分复用，多个事件流将 CPU 切割成多个时间片，不同事件流的时间片交替进行。I/O多路复用可以在单线程/进程中处理多个事件流。

select/poll/epoll 都是 I/O 多路复用的具体实现，select 出现的最早，之后是 poll，再是 epoll。



## 内核接收数据流程

首先编写一段基础的网络编程代码

```c++
// 创建socket
int s = socket(AF_INET, SOCK_STREAM, 0);   
// 绑定
bind(s, ...)
// 监听
listen(s, ...)
// 接受客户端连接
int c = accept(s, ...)
// 接收客户端数据
recv(c, ...);
// 将数据打印出来
printf(...)
```



计算机中运行着 A、B、C 三个进程，其中进程 A 执行着上述基础网络程序

<img src="http://store.secretcamp.cn/uPic/image-202104212241334802021042122413316190160937PqxNR7PqxNR.png" alt="image-20210421224133480" style="zoom:50%;" />

当进程 A 执行到创建 Socket 的语句时，操作系统会创建一个由文件系统管理的 Socket 对象。

Socket 对象有三个重要的结构，即发送缓冲区、接收缓冲区、等待队列三种结构。

其中，等待队列指向所有需要等待该 Socket 事件的进程。

<img src="http://store.secretcamp.cn/uPic/image-20210421224734252202104212247341619016454JyNfjoJyNfjo.png" alt="image-20210421224734252" style="zoom: 50%;" />



当程序执行到 `recv()` 时，操作系统会将进程 A 从工作队列移除（阻塞进程 A），并加入该 Socket 的等待队列中。工作队列此时只剩下了进程 B、C，依据进程调度，CPU 会轮流执行这两个进程的程序，不会执行进程 A 的程序。所以进程 A 被阻塞，不会往下执行代码，也不会占用 CPU 资源。

<img src="http://store.secretcamp.cn/uPic/image-20210421225119698202104212251191619016679IO5UCrIO5UCr.png" alt="image-20210421225119698" style="zoom:50%;" />



步骤①：CPU 轮流执行进程 B 和进程 C

步骤②：进程在 `recv()` 阻塞期间，计算机收到了某个 Socket 连接的客户端发送的数据。

步骤③：数据经由网卡通过硬件 DMA 的方式被写入到内核缓冲区，然后网卡通过中断信号通知 CPU 有数据到达，

步骤④：CPU 让出当前正在执行的程序，执行中断程序

步骤⑤：根据数据包的端口号，将数据写入到对应 Socket 的接收缓冲区里面。

> 由于一个 Socket 对应一个端口号，而网络数据包中包含了 IP 和端口的信息，内核可以通过端口号找到对应的 Socket 。

步骤⑥：找到 Socket 等待队列中的进程 A，唤醒进程 A，重新将进程 A 放入工作队列中，并将 A 从 Socket 等待队列中移除 。



<img src="http://store.secretcamp.cn/uPic/image-202104212254518882021042122545216190168922gaOHV2gaOHV.png" alt="image-20210421225451888" style="zoom:50%;" />

唤醒进程的过程如下:

<img src="http://store.secretcamp.cn/uPic/image-20210421230506882202104212305071619017507bSHXx2bSHXx2.png" alt="image-20210421230506882" style="zoom:60%;" />



以上是 `recv()` 监视单个 Socket 的流程，但是服务端需要管理多个客户端连接，于是人们开始寻找监视多个 Socket 的方法。

*select* 、 *poll* 、 *epoll* 都是 I/O 多路复用的具体实现。





## select

### 维护监听列表&阻塞进程

```c
int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```



先准备一个数组 fds，让该数组存放着所有需要监视的 Socket 的文件描述符，然后进程调用 `select()` ，将 fds 作为参数传入。

首先会遍历一遍 fds ，如果 fds 中的所有 Socket 都没有数据，`select()` 会使进程阻塞，直到有一个 Socket 接收到数据，`select()` 才会唤醒进程并返回。

```c
int s = socket(AF_INET, SOCK_STREAM, 0);  
bind(s, ...)
listen(s, ...)

int fds[] =  存放需要监听的socket的文件描述符

while(1){
    // 返回值是整形，代表有几个 socket 就绪了
    int n = select(..., fds, ...)
    for (int i = 0; i < fds.count; i++){
        if (FD_ISSET(fds[i], ...)) {
            // fds[i]的数据处理
        }
    }
}
```



假如进程同时监视如下图的 sock1 、sock2 和 sock3 一共三个 Socket，那么在调用 `select()` 之后，首先会遍历 fds ，如果某个 Socket 有数据，则会立即返回，如果没有，那么操作系统会将 A 从工作队列移除，并分别加入这三个 Socket 的等待队列中。进程 A 从工作队列中移除，不会再被分配到 CPU 时间片，这就是进程调用  `select()`  后被阻塞的原理。

<img src="http://store.secretcamp.cn/uPic/image-20210421232811534202104212328111619018891hm6V9ghm6V9g.png" alt="image-20210421232811534" style="zoom: 50%;" />





### 接收数据&唤醒进程

当与某个 Socket 连接的客户端发送完数据后，数据通过硬件 DMA 的方式被写入到内存，写入后会向 CPU 发出中断信号，CPU 会让正在执行的进程让出，去执行网络数据就绪的中断程序，将内存中的网络数据写入对应 Socket 的读缓冲区中，数据包中有相应的 IP 和端口，可以找到对应的 Socket 。

<img src="http://store.secretcamp.cn/uPic/image-20210421233239373202104212332391619019159q5JmYzq5JmYz.png" alt="image-20210421233239373" style="zoom:47%;" />



当任何一个 Socket 收到数据后，将触发中断程序，唤醒进程，即将进程从所有 Socket 的等待队列中移除，加入到工作队列里面，然后 `select()` 函数返回，返回值是整形，代表有几个 Socket 处于就绪状态。

当进程 A 被唤醒后，它知道至少有一个 Socket 接收了数据。程序只需遍历一遍 fds，检查每个 Socket 文件描述符的状态，就可以得到就绪的 Socket，然后做出处理，例如从线程池中分配一个线程去接收 Socket 读缓冲区中数据。

<img src="http://store.secretcamp.cn/uPic/image-20210421233536174202104212335361619019336uZpCIquZpCIq.png" alt="image-20210421233536174" style="zoom:50%;" />





### 分析

*select* 是最简单的 I/O 多路复用实现方法，但存在明显缺点：

- *select* 调用需要传入 fd 数组，需要拷贝一份到内核，高并发场景下这样的拷贝消耗的资源是惊人的。

- 每次调用 *select* 都需要将进程加入到所有监视 Socket 的等待队列，每次唤醒都需要从每个队列中移除。这里涉及了两次遍历，有一定的开销。正是因为遍历操作开销大，出于效率的考量，才会规定 Select 的最大监视数量，默认只能监视 1024 个 socket 。
- 进程被唤醒后，程序并不知道哪些 Socket 收到数据，还需要遍历一次。
- *select* 想要一直监听，只能写在 while 循环里，像「将进程加入 socket 的等待队列」以及「将进程从 socket 等待队列移除 」 这样的操作每次调用 select 就会不停进行上述遍历，非常低效，归根到底是因为 *select* 不记录状态。

一次 *select* 总共需要三次遍历

- 将进程加入所有 socket 的等待队列
- 将进程从所有 socket 的等待队列中移除
- 遍历所有 socket ，看哪个 socket 的缓冲区内有数据



## poll

```c
int poll(struct pollfd *fds, unsigned int nfds, int timeout);
```

poll 本质上和 select 没有区别，它将用户传入的数组 fds 拷贝到内核空间，然后查询每个 fd 对应的状态，但是它没有最大连接数的限制。

poll 中传入的数字 fds 是 pollfd 类型的数组，pollfd 的定义如下：

```c
struct pollfd {
     int   fd;         /* file descriptor */
     short events;     /* requested events */
     short revents;    /* returned events */
};
```



## epoll

### 设计思路

#### select的缺陷

*select* 低效的原因主要有两个：

1. *select* 将 “维护等待队列” 和 “阻塞唤醒进程” 两个步骤合二为一，因为 select 函数是一个单独的函数，执行完功能后不会保存状态，所以每次调用都要把需要监听的 socket 数组传递给它，这是非常低效的。

   *epoll* 将这两个操作分开，先用 `epoll_ctl()` 维护等待队列，再调用 `epoll_wait()` 阻塞唤醒进程。



2. *select* 的返回值是 int 类型，只能代表有几个 socket 就绪了，程序不知道哪些 socket 收到数据，只能一个个遍历。如果内核维护一个 “就绪列表”，引用收到数据的 socket ，就能避免遍历。

   这一点是 epoll 是通过维护 eventpoll 对象中的就序列表实现的。

<img src="http://store.secretcamp.cn/uPic/image-20210422212928938202104222129291619098169shXCThshXCTh.png" alt="image-20210422212928938" style="zoom:50%;" />



#### epoll的总体思路

epoll 先用 `epoll_create()` 创建一个代表 epoll 的 eventpoll 对象，再通过 `epoll_ctl()` 将需要监视的 socket 添加到 eventpoll 中，最后调用 `epoll_wait` 等待数据。

在 epoll 中，因为可以复用 eventpoll 对象，仅需要一次遍历（而且还是红黑树操作），而 select 需要三次遍历，效率明显提高。

```c
int s = socket(AF_INET, SOCK_STREAM, 0);   
bind(s, ...)
listen(s, ...)

int epfd = epoll_create(...);  // 创建一个 eventpoll 对象，系统会返回一个 eventpoll 对象的 id
epoll_ctl(epfd, ...); // 将所有需要监听的 socket 添加到 epfd 中

while (1) {
    int n = epoll_wait(epfd, events)
    for (int event : events) {
        //处理
    }
}
```



### eventpoll数据结构

epoll 在内核空间内，有一个对应的数据结构存储相关信息，这个数据结构实际上就是 eventpoll 对象。

eventpoll 对象可以通过系统函数 `epoll_create()` 去创建，并返回一个 eventpoll 对象的文件描述符 epfd 。

```c
int epoll_create(int size);
```





eventpoll 主要有三块重要的区域：

1. 监听队列 rbr（红黑树），用于存放需要监听的 socket_fd 
2. 就序队列 rdlist（双向链表），存放处于就绪状态的 socket 信息
3. 等待队列 wq，保存了调用 `epoll_wait()` 函数的进程



另外，epoll 还提供了两个函数 `epoll_ctl()` 和 `epoll_wait()` 

- `epoll_ctl()` 可以去增删改内核空间中 eventpoll 对象的监听列表

- `epoll_wait()` 主要参数是 eventpoll 对象，传入用于获取此次系统调用需要监听的 socket_fds，默认情况下会阻塞调用进程，直到 eventpoll 中关联的某个 socket 就绪之后，`epoll_wait()` 才会返回。

<img src="http://store.secretcamp.cn/uPic/image-20210816143412079202108161434121629095652vEj4EqvEj4Eq.png" alt="image-20210816143412079" style="zoom:40%;" />



#### epoll_ctl

epfd： `epoll_create()` 函数的返回值，即 eventpoll 对象

op：表示动作，即增删改

fd：需要监听的 socket 的文件描述符

*event：需要监听的事件

```c
int epoll_ctl (int epfd, int op, int fd, struct epoll_event *event);
```



#### epoll_wait

epfd： `epoll_create()` 函数的返回值即 eventpoll 对象的文件描述符。

```c
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
```



### 创建epoll对象

当某个进程调用 `epoll_create()` 方法时，内核会创建一个代表该 epoll 的 eventpoll ，并返回一个 eventpoll 对象的文件描述符 epfd ，相当于在内核中开辟了一小块内存空间，这个内存空间的位置是已知的。

eventpoll 对象也是文件系统中的一员，和 socket 一样，它也会维护一个等待队列。eventpoll 对象相当于是 socket 和进程之间的中介，socket 的数据接收并不直接影响进程，而是通过改变 eventpoll 的就绪列表 rdlist 来改变进程状态。



### 维护监听队列

创建 epoll 对象后，可以用 `epoll_ctl()` 添加或删除所要监听的 socket ，首先会将 socket_fd 添加到 eventepoll 对象的监听队列 rbr 中，然后内核程序会根据将 eventpoll 添加到这三个 socket 的等待队列中。

这里避免了 select 中将进程从 socket 等待队列中移除的操作，取而代之的是 eventpoll 会被加入所有 socket 的等待队列，且只有第一次需要这样操作，以后每次调用 `epoll_wait()` 也都没必要移除它，这就减少了一次循环操作。

<img src="http://store.secretcamp.cn/uPic/image-20210422225912683202104222259121619103552DC34R9DC34R9.png" alt="image-20210422225912683" style="zoom:50%;" />





### 阻塞进程

当程序执行到 `epoll_wait()` 时，如果就绪队列 rdlist 中没有任何引用，那么内核会将进程 A 放入 eventpoll 的等待队列 wq 中，并阻塞该进程。

<img src="http://store.secretcamp.cn/uPic/image-202104222146245832021042221462416190991843VLa4x3VLa4x.png" alt="image-20210422214624583" style="zoom:50%;" />



### 接收数据&唤醒进程

epoll 中接收数据的过程与 select 相似，当与某个 socket 连接的客户端发送完数据后，数据通过硬件 DMA 的方式被写入到内存，之后相应的硬件会向 CPU 发出中断信号。CPU 会让正在执行的进程让出，去执行网络数据就绪的中断程序，将内存中的网络数据写入对应 socket 的读缓冲区中。

当 socket 的读缓冲区接收到数据，中断程序会去检查 socket 的等待队列，发现 socket 等待队列中引用的不是一个进程，而是一个 eventpoll 对象，于是中断程序会操作 eventpoll 对象，一方面修改 eventpoll 的就绪队列 rdlist，将就绪的 socket 加入就序列表 rdlist 之中，另一方面检查 eventpoll 的等待队列 wq，如果有进程，就将进程加入到内核的工作队列中，唤醒进程。

<img src="http://store.secretcamp.cn/uPic/image-20210422214200712202104222142001619098920rcH9rKrcH9rK.png" alt="image-20210422214200712" style="zoom:50%;" />

接下来就是进行 read 操作，根据 epoll_wait 的返回值找到 eventpoll 对象，因为有 eventpoll 的就绪列表 rdlist 的存在，进程 A 可以知道哪些 socket 接收到了数据，不需要像 select 一样遍历。

`epoll_wait()` 函数在调用的时候，会传入一个 epoll_event 事件数组指针，`epoll_wait()` 在返回之前，会将就绪的 socket 事件信息拷贝到这个指针表示的数组中，这样就可以通过这个数组拿到就绪的 socket 信息了。

<img src="http://store.secretcamp.cn/uPic/image-20210422214719189202104222147191619099239C20c1nC20c1n.png" alt="image-20210422214719189" style="zoom:50%;" />





## 扩展问题

### select如何得知socket有数据到达？

网络中数据通过 DMA 的方式拷贝到内核缓冲区后，会触发 DMA 硬件中断，CPU 会立刻执行中断程序，根据内存中的数据包中的端口号，确定是哪个 socket 的实例，然后将数据从内核缓冲区复制 socket 的读缓冲区中，然后会去检查当前 socket 的等待队列是否有正在等待的进程，如果有，则将该进程移入 CPU 的工作队列中，然后当前进程再次执行 `select()` 函数去检查是否存在就绪的 socket。



### select和poll的区别？

`select()` 使用的是 fd_set，内核中规定了 `#define __FD_SETSIZE = 1024` ，即该数组最大长度为 1024 ，要修改只能重新编译内核。`poll()` 使用的是自定义的 pollfd 数组结构，没有长度为 1024 的限制。



### epoll_wait可以设置成非阻塞吗？

可以，`epoll_wait()` 有一个 int 类型的 timeout 参数，表示阻塞时间的长度，如果设置为 0 ，那么就是非阻塞调用的，每次调用都会去检查 eventpoll 的就序列表。



### eventpoll的监听列表的数据结构是什么？

eventpoll 的监听列表 rbr（并不是一个列表）用于存放需要监听的 socket 的集合信息，采用的是红黑树结构，因为这个 socket 集合信息经常需要进行增删改查。

Socket 指向 eventpoll，是因为数据达到是要通过 Socket 找到对应 eventpoll

rbr 指向 Socket，是因为 `epoll_control` 调用时要通过 eventpoll 找到 Socket ，然后再删除 Socket 等待队列的引用。



### IO多路复用是异步的吗？

异步还是同步，需要分层去看待。

epoll 系统调用，是同步的，也就是必须等待操作系统返回值，socket 数据没有就绪前会阻塞当前进程。

底层调用 epoll 的封装后的框架，可以是异步的，例如 Java NIO，只要你暴露给外部的接口，无需等待你的返回值即可，即客户端无需等待服务端的返回。

而在 epoll 这个系统调用的底层内核设计里，每个 IO 事件的通知等待，是异步的，例如网络数据到达是通过 CPU 中断唤醒被阻塞的进程，而不是轮询检查是否有数据到达。但这不影响，epoll 这个系统调用对外部来说，是一个同步的接口。

所以异步还是同步需要分层去看待，一个操作对外是同步的，但底层可能是异步的，反之亦然。
