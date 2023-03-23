# CS162 Lecture 1: What is an Operating System?

- 一些课程提供的资料：
  - ![image-20220712165459645](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207121655779.png)
- 教学大纲
  - ![image-20220712165524048](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207121655112.png)
- Homewok & Project
  - ![image-20220712165635837](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207121656911.png)
- What is a OS?
  - ![image-20220712171723257](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207121717309.png)
  - ![image-20220712171707991](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207121717063.png)

# CS162 Lecture 2: Four Fundamental OS Concepts

- OS提供了位于应用和物理机器之间的一层抽象机器。
  - 处理器：抽象为线程
  - 内存：地址空间
  - 磁盘、固态：文件
  - 网络：Socket
  - 机器：进程
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131343362.png" alt="image-20220713134314241" style="zoom:67%;" />
- 四个基础的OS概念：
  - 线程：程序的执行上下文。
    - 完整描述程序状态，包含PC的值、寄存器的值、标志位的值（0、溢出,etc）、栈
  - 地址空间（不管有或者没有地址翻译）
    - 程序能访问的一系列的内存地址（用于读写）
    - 可能和物理机器的内存空间不同（使用虚拟地址）
  - 进程：一个正在运行的程序的实例
    - 受保护的地址空间以及一个或多个线程
  - 双模式运转：
    - 一个典型的处理器有两种状态：用户态和内核态
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131345895.png" alt="image-20220713134510818" style="zoom:67%;" />

## First OS Concept: Thread of Control

- 线程：单个执行上下文。
  - 程序计数器、寄存器、标志位、栈、内存状态
- 当一个线程驻留处理器中时，它就处于执行中的状态。
- 什么叫驻留于处理器中：
  - 寄存器保存了线程的上下文
    - 包含PC、当前正在执行的指令。
  - 包含用于正在进行的计算的立即数。有真的值也有指针值。
  - 栈指针指向栈顶。
  - 其余的位于内存中。
- 当一个线程的状态没有被加载到处理器中时，它就是被挂起的。
  - 此时处理器指向其他的线程。
  - 当一个线程被挂起时，它被保存在内存中，以TCB($Thread \ Control \ Block$)的形式。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131418075.png" alt="image-20220713141801983" style="zoom:67%;" />

- 可以将线程看做位于一个个的虚拟核上。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131427942.png" alt="image-20220713142748867" style="zoom:67%;" />
- 情境演示，当发生上下文切换时发生了什么：
  - 宏观上来看，从T1切换到T2的这段时间，发生的是上下文切换。
  - 具体来说，将`vCPU1`的PC的值、栈指针的值等等存入`vCPU1`的TCB放入内存。
  - 将位于`vCPU2`的TCB中的PC的值/栈指针等等加载到对应的地方（寄存器等）。
  - **目前暂且可以认为TCB是存储在内核所在的内存空间中的**
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131429278.png" alt="image-20220713142908190" style="zoom:67%;" />

- 当有多个线程时，内存中是什么样的（线程这个抽象能够带给我们什么？）：
  - 每个线程都有自己的堆（堆是线程之间共享的）、栈、数据、指令
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131440749.png" alt="image-20220713144032648" style="zoom:67%;" />

## Second OS Concept: Address Space

- 一个线程的内存是分段的：
  - 代码段：代码
  - 静态数据段：全局变量、静态变量、字符串等
  - 栈：本地变量（比如当函数调用前将本地的变量入栈）
  - 堆：使用`malloc()`分配的内存，比如带指针的结构，链表之类的。
  - 关于堆和栈的分配，以后再说。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131554912.png" alt="image-20220713155410805" style="zoom:67%;" />

- 如果采用最原始的，所有的`vCPU`共享所有的非CPU资源，比如I/O、内存。那么会存在很多安全问题。
  - 每个线程可能会覆写其他线程的内存，甚至是覆写操作系统的内存。
  - 这种只使用物理内存空间的方法用于早期的操作系统，但是这种方法风险很大。
  - 所以我们需要操作系统提供一些保护措施。
- 简单的复用是没有保护措施的。
  - 操作系统必须保护自身免受其他用户程序的危害。
    - 限制线程能够访问的数据以及线程能够做的事情的尺度。
    - 保证每个线程都能够分到合适的系统资源比例（公平性，不会饿死是底线）。
  - 操作系统同样需要保证用户程序彼此之间不存在危害。
  - ![image-20220713160523548](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131605640.png)

### Simple Protection: Base and Bound (B&B)

- 借助硬件帮助OS提供保护。
- Base寄存器保存线程能够访问的内存的起始地址，Bound寄存器保存程序能够访问的内存的上限地址。
  - 每当要访问一个地址前，先检查其是否处于$[Base,Bound)$区间内。不处于的话可能就杀掉进程。
  - 但是由于可执行文件中的所有的地址都看上去是从地址`00000000`开始加载的，所以需要一个`重定位加载器`（软件性的重定位）。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131623288.png" alt="image-20220713162341210" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131608432.png" alt="image-20220713160857343" style="zoom: 80%;" />

### Simple address translation with Base and Bound

- 基于上一个的基础上，使用硬件来做地址转换（那个加号就是）。
- 传入的地址类似偏移量，由硬件加上基址做地址转换。地址合法性检查同前。
- Bound这里可以是偏移量的上限，也可以是物理地址的上限。前者在转换之前检查地址合法性，后者在转换之后检查地址合法性。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131639954.png" alt="image-20220713163902857" style="zoom:80%;" />

### Another idea: Address Space Translation

- 程序运行在和物理内存完全分离的地址空间上。虚拟地址经过硬件翻译得到物理地址。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131710679.png" alt="image-20220713171017584" style="zoom:67%;" />

- 分页虚拟地址空间

  - 将整个虚拟地址空间划分为等尺寸的块（页），每一页有一个`Base`
  - 每页的大小一样，所以很容易把每页放入内存。
  - 硬件利用页表实现地址翻译。
  - 每页有一个独立的`Base`，每页的`Bound`是页面的尺寸。
  - 特殊的硬件寄存器保存指向页表的指针。
  - **将内存也分页为与虚拟页等尺寸的帧，这样就可以把任意页放入任意空闲帧**
  - 分页后，指令运行在虚拟地址上。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131711366.png" alt="image-20220713171121273" style="zoom:67%;" />

  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207131715030.png" alt="image-20220713171528931" style="zoom:67%;" />

## Third OS Concept: Process

- 一个进程是具有受限制的权利的执行环境。
- 一个进程包含的资源有：
  - 受保护的地址空间，带有一个或多个线程。
  - 内存（地址空间）
  - 文件描述符、文件系统上下文，etc
  - 进程将一个或多个**共享进程资源**的线程封装起来。
- 应用程序作为进程启动，应用程序还可以启动子进程（使用`fork()`/`exec()`。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207140943847.png" alt="image-20220714094253633" style="zoom:80%;" />
- 为什么需要进程？
  - 它提供了受保护的地址空间，能够使得进程与进程之间相互隔离开（不互相伤害，比如覆写对方内存），能保护操作系统不受其他进程危害。
  - 进程之内的线程之间是有可能覆写内存的。（所以我们才需要锁这样的东西来保证同步与互斥）
- 在保护性与效率之间存在一个很重要的`tradeoff`，在进程内的通信更加容易（但是更危险），在进程之间的通信更难（但是更安全）
- 单线程和多线程的进程：
  - 不管是单线程还是多线程。**每个线程都有属于自己的栈（因为每个线程至少都需要保存自己的函数调用栈）**
  - **堆是线程之间共享的，全局变量、静态变量、静态字符串等等都保存在堆区**。
    - 堆肯定是共享的啊。举个例子，全局变量是保存在堆的，全局变量是线程之间共享的，假如每个线程都有属于自己的堆，那怎么共享全局变量。
  - 线程封装了并发（还有并行）。
  - 地址空间封装了保护，使得有bug的软件不会导致系统崩溃。
  - 我们为什么需要多线程？
    - 并行性。如果有多个核，那么使用多线程，每个核上运行一个线程，这样可以利用硬件优势提高针对同一个任务的效率。
      - 并行利用实际硬件上的并行性，比如多核。也就是说，**实际上的并行的概念只存在于多核的CPU上**
      - **同一个时刻，有多个进程运行在多个核上**。
    - 并发性。同时处理多个事件。比如一个线程负责监听鼠标事件，一个线程负责监听窗口事件，一个线程负责网络连接。
      - **对于单核CPU，同一个时刻，只有一个进程在核上，但是给用户提供的假象是多个进程同时运行**，
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207140947196.png" alt="image-20220714094759028" style="zoom:67%;" />
- Why do we need Process?Protection and Isolation.
  - ![image-20220714095549569](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207140955719.png)

### 2022.11.8补充：并行与并发的区别

无论是单CPU还是多CPU，操作系统都营造出了可以同时运行多个程序的**假象**。实际的过程操作系统对进程的调度以及CPU的快速上下文切换实现的：**每个进程执行一会就先停下来，然后CPU切换到下个被操作系统调度到的进程上使之运行**。因为切换的很快，使得用户认为操作系统一直在服务自己的程序。

并发(concurrent)指的是多个程序可以同时运行的现象，更细化的是多进程可以同时运行或者多指令可以同时运行。但这不是重点，在描述并发的时候也不会去扣这种字眼是否精确，并发的重点在于它是一种现象。并发描述的是多进程同时运行的现象。但实际上，对于单核心CPU来说，同一时刻只能运行一个进程。所以，这里的"同时运行"表示的不是真的同一时刻有多个进程运行的现象（**而是同一时刻有多个进程运行的假象**），这是并行的概念（对应的，假象是并发的概念），而是提供一种功能让用户看来多个程序同时运行起来了，但实际上这些程序中的进程不是一直霸占CPU的，而是执行一会停一会。

所以，并发和并行的区别就很明显了。**它们虽然都说是"多个进程同时运行"，但是它们的"同时"不是一个概念。并行的"同时"是同一时刻可以多个进程在运行(处于running)，并发的"同时"是经过上下文快速切换，使得看上去多个进程同时都在运行的现象，是一种OS欺骗用户的现象**。

实际上，**当程序中写下多进程或多线程代码时，这意味着的是并发而不是并行**。并发是因为多进程/多线程都是需要去完成的任务，不并行是因为并行与否由操作系统的调度器决定，可能会让多个进程/线程被调度到同一个CPU核心上。只不过调度算法会尽量让不同进程/线程使用不同的CPU核心，所以在实际使用中几乎总是会并行，但却不能以100%的角度去保证会并行。也就是说，**并行与否程序员无法控制，只能让操作系统决定**。

再次注明，并发是一种现象，之所以能有这种现象的存在，和CPU的多少无关，而是和进程调度以及上下文切换有关的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211081551646.png" alt="image-20221108155105289" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211081551309.png" alt="image-20221108155134031" style="zoom:67%;" />

## Fourth OS Concept:  Dual Mode Operation

- 注：`w/:with`,`w/o: without`。

- 硬件至少提供两种运行模式（至少一比特的模式位）

  - 内核模式，内核态。（监管者模式，超级用户模式？）
  - 用户模式，用户态。

- 当处于用户态时，某些特定的操作是被禁止的。

  - 比如改变页表指针，关闭中断，直接与硬件交互，写入内核内存。

- 用户态与内核态的转换是严格受控的。

  - 中断、系统调用、异常。

  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141014411.png" alt="image-20220714101408224" style="zoom:80%;" />

- 以Unix的宏内核为例:

  - 应用、标准库等等处于用户态。
  - 系统调用、中断、陷阱、异常等属于内核态。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141018300.png" alt="image-20220714101841138" style="zoom:80%;" />

- 用户态下有受限的硬件(HW)访问，内核态下则是完全的访问权限。

  - `exec()`:内核态===>用户态
  - 系统调用:用户态===>内核态
  - `rtn`:指的是从内核态的`return`。
  - 中断:用户态==>内核态
  - 其他的看图吧。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141020955.png" alt="image-20220714102006825" style="zoom: 67%;" />

- 能够通过容器技术和虚拟机等提供额外的保护：

  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141025853.png" alt="image-20220714102514701" style="zoom:80%;" />

- 三类 用户态$\Rightarrow$内核态的转换：

  - 系统调用：类似函数调用，但是是在进程外部的。当进程需要操作系统服务时。
  - 中断：外部非同步的事件触发。
  - 陷阱或者异常：进程内部的同步事件触发，如除0、`segmentation fault`。
  - **三种都是unprogrammed control transfer**（未编程的控制转移？）
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141026623.png" alt="image-20220714102615481" style="zoom:80%;" />

- Process Control Block：PCB，进程控制块。
  - PCB保存了进程的状态信息，内核以PCB的形式代表每个进程。
    - 进程的状态：正在运行、就绪、阻塞,etc。
    - 进程的PID、用户、可执行文件、优先级
    - 执行时间
    - 内存空间...
  - 内核调度器维持一个包含许多PCB的数据结构。
  - 调度策略选择下一个要运行的进程。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141029563.png" alt="image-20220714102956440" style="zoom:80%;" />

## Conclusion: Four Fundamental OS Concepts

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141045234.png" alt="image-20220714104514072" style="zoom:80%;" />

# CS162 Lecture 3: Abstractions 1: Threads and Processes

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141046144.png" alt="image-20220714104604015" style="zoom: 80%;" />

- Goals for today:
  - **What** threads are
    - –And what they are not
  - **Why** threads are useful (motivation)
  - **How** to write a program using threads
  - **Alternatives** to using threads



## What Threads Are

- 线程：单个的执行上下文。
- 线程：代表一个能被**独立调度**的任务的执行序列。
- 线程可以用于并发，也可以用于并行。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141544546.png" alt="image-20220714154448374" style="zoom: 67%;" />



## Motivation for Threads

- 概括起来就是我们需求一个程序能一次处理多件事。

- Threads are a unit of *concurrency* provided by the OS

  Each thread can represent one thing or one task

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141550036.png" alt="image-20220714155013916" style="zoom:67%;" />

## Multiprocessing vs. Multiprogramming

- Multiprocessing：有多个CPU或者核心。
  - 多进程（MultiProcessing）强调的场景则是至少两个 CPU（或者多个 CPU），在这种情况下是真真正正的多个 CPU 同时并发地工作，虽然每个 CPU 可能依然负责在多个任务之间轮转，但是对于系统来说，现在是真的并发执行了（而不是伪并发），因为可以在同一个时刻处理两个甚至多个任务了。

- Multiprogramming：多个任务或进程，不一定要同时进行。
  - 多程序（MultiProgramming）强调的场景是，当只有一个 CPU 的时候，可以运行多个程序 program 让整个系统看起来是并发的；而其实一个 CPU 并不能够完成真正的并发，所谓的多个 program 同时运行也不过是 CPU 在不同任务之间轮转的表象。


<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141553782.png" alt="image-20220714155331674" style="zoom: 80%;" />

- 并发与并行的区别：直接看Lecture2的补充吧
  - 并发是一次能够处理多件事情，而并行是指同一个时刻有多个任务同时执行。
  - 例如，两个线程运行在一个单核CPU上，这两个线程是并发的，但是并不是并行的（因为是两个线程轮流切换）。
  - 每个线程能处理一个独立的任务，但是这些任务并不一定是同时执行的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141603707.png" alt="image-20220714160331599" style="zoom:67%;" />

## Threads Mask I/O Latency

- 一个线程的三个状态：正在运行、就绪、阻塞
- 如果一个线程在等待I/O完成，那么OS会将其标记为阻塞，并且将其移除到就绪列表。当I/O完成，OS将其标记为就绪。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141622404.png" alt="image-20220714162201315" style="zoom: 60%;" />
- **一个被阻塞的线程并不能直接从阻塞态跳转到运行态，因为需要给调度器一个调度它的机会**，不能越过调度器直接运行。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141618840.png" alt="image-20220714161832740" style="zoom: 67%;" />

## OS Library API for Threads: *pthreads*

- P：指的是[POSIX](https://baike.baidu.com/item/%E5%8F%AF%E7%A7%BB%E6%A4%8D%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E6%8E%A5%E5%8F%A3/12718298?fromtitle=POSIX&fromid=3792413&fr=aladdin)。
- **可移植操作系统接口**（英语：Portable Operating System Interface，缩写为**POSIX**）是[IEEE](https://baike.baidu.com/item/IEEE)为要在各种[UNIX](https://baike.baidu.com/item/UNIX)[操作系统](https://baike.baidu.com/item/操作系统)上运行软件，而定义API的一系列互相关联的标准的总称
- 但是其实目前并没有完全标准化的接口。
- 关于C的函数指针[Function Pointer in C](https://www.geeksforgeeks.org/function-pointer-in-c/)

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,void *(*start_routine)(void*), void *arg);
//void *(*start_routine)(void*)
//函数指针，指向一个函数void* func(void* )

int pthread_join(pthread_t thread, void **value_ptr);
//暂停调用线程的执行，直到目标线程终止。
```

![image-20220714172035800](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141720923.png)

- What happens when pthread_create(…) is called in a process?
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141737228.png" alt="image-20220714173728124" style="zoom:67%;" />
- Fork-Join Pattern：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141737162.png" alt="image-20220714173752054" style="zoom:67%;" />

## Thread State

- 线程间共享的：
  - 共享内存（全局变量、**堆(Heap)**、静态变量）
  - I/O状态（文件描述符、**网络连接**,ect）
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141740675.png" alt="image-20220714174026576" style="zoom: 80%;" />
- 线程私有的：
  - 都保存在TCB中
  - 寄存器的值，包括PC
  - 调用栈
- 调用栈：
  - 参数、临时变量
  - 函数调用的返回位置

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141753865.png" alt="image-20220714175317749" style="zoom:67%;" />

## Thread Abstraction

- 程序员视角：有无限多个处理器，可以满足多线程的要求。
- 现实：有一部分线程在运行，有一部分线程没有运行，线程轮换。
- 程序员期待的代码运行于实际调度的不确定性：
  - 程序员看来每行代码是连续执行的，但是在实际的调度中，可能确实是连续执行的，也可能是执行一行就被挂起。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141804787.png" alt="image-20220714180455671" style="zoom: 67%;" />

## Correctness with Concurrent Threads

- 非确定性：
  - 调度器可以以任何顺序运行线程。
  - 调度器可以在任何时候切换线程。
  - 这使得测试非常困难。
  - 但是我们要保证我们的程序在这种非确定性之下永远是正确的。
- 独立线程：
  - 线程之间没有共享的状态
  - 确定性的、可以复现的情况
- 协作线程：
  - 多个线程之间共享状态。

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141806125.png" alt="image-20220714180617033" style="zoom:67%;" />

## Race Conditions

- 不存在Race Condition：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141917029.png" alt="image-20220714191747923" style="zoom:67%;" />
- 存在Race Condition：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141918269.png" alt="image-20220714191809174" style="zoom: 67%;" />

## Relevant Definitions(关于锁和同步的)

- 同步：线程之间的协作，通常与共享的数据有关。
- 互斥：保证一次只有一个线程能做某个特定的操作
- 临界区：一次只能有一个线程运行的代码
- 锁：一次只能有一个线程持有的对象。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141919671.png" alt="image-20220714191906567" style="zoom:67%;" />



## Locks

- 锁提供两个原子操作：
  - 获取锁：等待直到锁是空闲的，然后将其标记为占用。称作这个线程持有这个锁。
  - 释放锁：只能被目前持有这个锁的线程调用。调用返回后，这个线程不再持有这个锁。
- 目前暂不关心锁的具体实现。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141948898.png" alt="image-20220714194832793" style="zoom:67%;" />

## OS Library Locks: *pthreads*

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207141950618.png" alt="image-20220714195040516" style="zoom:67%;" />

## Processes

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207142011692.png" alt="image-20220714201130594" style="zoom:80%;" />

- 所有的进程都是被其他进程创建的，那么第一个进程是如何来的？
  - `Bootstrapping`：自举。
  - 第一个进程是被内核启动的，常称作`init`。在此之后所有的进程都被其他进程所创建。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150914103.png" alt="image-20220715091436908" style="zoom: 80%;" />

## Process Management API

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150918074.png" alt="image-20220715091858929" style="zoom:67%;" />

### exit()

- 当没有显式调用`exit()`时，OS库会隐式调用。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150920201.png" alt="image-20220715092014032" style="zoom:80%;" />

### fork()

- `fork()`会将当前进程的内存复制一个副本，放入一个新的地址空间。
  - 新的进程有不同的PID，但是内存和父进程是完全一样的。
  - 新的进程最开始只有一个线程。
  - `fork()`在子进程中返回0，在父进程中返回子进程的PID。
  - 如果`fork()`失败，则返回一个负数。
  - 子进程和父进程之间构成一棵进程树，如果子进程再`fork()`则它也变为父进程
  - 当父进程被`kill()`，子进程不会被`kill()`，会被父进程的父进程接管，一直`Kill()`最终由`init`进程接管。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150923297.png" alt="image-20220715092331114" style="zoom: 67%;" />

- C:Child，P:Parent
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150927729.png" alt="image-20220715092731554" style="zoom: 60%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150928490.png" alt="image-20220715092809318" style="zoom:60%;" />

- 在这种情况下两个`i`并不会发生竞争冒险，因为是不同的`i`，处于不同的地址空间。但是输出的顺序会因为调度的原因变化多样。
  - 完全独立的地址空间，因为是两个进程而不是线程。所以只存在对标准输出流的竞争。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150933928.png" alt="image-20220715093329735" style="zoom:67%;" />

### exec()

- `exec()`会将当前进程的内存内容全部清空，使用新进程覆盖。
- 下图的情况下，正常情况下`execv()`是不会`return`的（都全部覆盖了还return干嘛）。只有出错的时候才会`return`。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151005384.png" alt="image-20220715100537180" style="zoom:67%;" />
- `Shell`是如何利用`fork()`和`exec()`实现的：
  - 每当敲入一个命令，终端`fork`出一个新的终端，然后新的终端`exec`出命令对应的进程，旧终端则等待其返回。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151007207.png" alt="image-20220715100744017" style="zoom:67%;" />

### wait()

- `wait()`会一直等待子进程直到其结束，并将子进程`exit()`的参数值填充到`status`中去（图中是42），同时返回子进程的`PID`。
- 如果有多个子进程，可以指定等待哪一个子进程。`wait()`能起作用是因为内核维护了父子进程关系。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151009986.png" alt="image-20220715100944814" style="zoom:67%;" />

### kill() & sigaction()

- `sigation()`：设定收到相应的信号时的处理器。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151020424.png" alt="image-20220715102033267" style="zoom:67%;" />

- POSIX SIGNAL：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151023717.png" alt="image-20220715102305568" style="zoom:67%;" />
- 下面这个当收到`Ctrl + C`时会调用`signal_callback_handler()`

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151022931.png" alt="image-20220715102226740" style="zoom:67%;" />



## Conclusion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207150937087.png" alt="image-20220715093743899" style="zoom:80%;" />

# CS162 Lecture 4: Abstractions 2: Files and I/O

![image-20220715102856207](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151028413.png)

- Goals for Today: The File Abstraction
  - High-Level File I/O: Streams
  - Low-Level File I/O: File Descriptors
  - *How* and *Why* of High-Level File I/O
  - Process State for File Descriptors
  - Common Pitfalls with OS Abstractions

## Unix/POSIX Idea: Everything is a “File”

- 一切都是文件，因此有很多东西有相似的接口
  - 硬盘上的文件
  - 设备
  - 普通文件
  - 网络（Socket）
  - 本地进程间通信的手段
- 这些接口是基于系统调用的：`open()、read()、write()、close()`
- `ioctl()`用于配置那些不太符合的。
- 一切皆文件的思想在提出来的时候是相当激进的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151057805.png" alt="image-20220715105725593" style="zoom:67%;" />

## The File System Abstraction

- 文件：
  - 一个字节流序列，可以是文本文件、二进制文件、序列化对象。
  - 元数据：描述文件本身的数据，包括其大小、最后一次编辑的时间、所有者、安全信息、访问权限等。
- 目录：
  - 包含文件和目录的文件夹。
  - 在内核中，文件夹也只是一个将文件名与实际的文件内容映射起来的一个文件。
  - 目前暂时不深入讲解。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151104711.png" alt="image-20220715110445508" style="zoom:67%;" />

## Connecting Processes, File Systems, and Users

- 每个进程都有一个工作目录($CWD$)，工作目录可以使用系统调用更改。
- 绝对路径会忽略掉$CWD$。
- 相对路径是相对$CWD$来说的。
- `~`是`home`目录。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151504704.png" alt="image-20220715150403517" style="zoom: 67%;" />

## C High-Level File API – Streams

- 字节流：未格式化的字节序列（不存在关于它是文本文件还是二进制数据的信息），带有一个指针指向当前阅读的位置。
- 打开的字节流使用一个`FILE`结构体指针来代表。
- `fopen()`返回的是一个`FILE`结构体指针，打开文件后的后续操作都是使用这个指向文件的指针。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151514270.png" alt="image-20220715151416132" style="zoom:67%;" />

## C API Standard Streams – stdio.h

- 三种预定义的标准流：
  - 标准输出流：一般是屏幕
  - 标准输入流：一般是键盘
  - 标准错误流：一般也是屏幕
- 三种标准流都是可以被重定向的。比如使用管道进行输入重定向和输出重定向。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151545084.png" alt="image-20220715154518953" style="zoom:67%;" />

## C High-Level File API

```c
// character oriented  
//返回值是int而不是char,是因为EOF是-1，-1并不是任何一个char
int fputc( int c, FILE *fp );			// rtn c or EOF on err,put a char
int fputs( const char *s, FILE *fp );	// rtn > 0 or EOF,put a String

int fgetc( FILE * fp );
char *fgets( char *buf, int n, FILE *fp );

// block oriented
//一块一块的读取或写入字节流，在这种情况下需要一个缓冲区。
//从左往右第一个参数：缓冲区的起始地址,缓冲区的大小最小为size_of_elements * number_of_elements(元素的个数乘以单个元素的大小)
//第二个参数：元素的大小
//第三个参数：元素的个数
//返回值：读取到的元素的个数，如果返回值小于number_of_elements，要么到达EOF，要么出错了
size_t fread(void *ptr, size_t size_of_elements, 
             size_t number_of_elements, FILE *a_file);
size_t fwrite(const void *ptr, size_t size_of_elements, 
             size_t number_of_elements, FILE *a_file);

// formatted
int fprintf(FILE *restrict stream, const char *restrict format, ...);
int fscanf(FILE *restrict stream, const char *restrict format, ... );

```

```c
int main(void) {
  FILE* input = fopen(“input.txt”, “r”);
  FILE* output = fopen(“output.txt”, “w”);
  int c;
//这里使用int来接收读取到的字符是因为fgetc()会返回EOF,而EOF不是一个char,EOF是-1
  c = fgetc(input);
  while (c != EOF) {
    fputc(output, c);
    c = fgetc(input);
  }
  fclose(input);
  fclose(output);
}

#define BUFFER_SIZE 1024
int main(void) {
  FILE* input = fopen("input.txt", "r");
  FILE* output = fopen("output.txt", "w");
  char buffer[BUFFER_SIZE];
  size_t length;
  length = fread(buffer, sizeof(char), BUFFER_SIZE / sizeof(char), input);
  while (length > 0) {
    fwrite(buffer, length, sizeof(char), output);
    length = fread(buffer, sizeof(char), BUFFER_SIZE / sizeof(char), input);
  }
  fclose(input);
  fclose(output);
}
```

## C High-Level File API: Positioning The Pointer

- 用于调整指针位置的`fseek()`
  - 针对偏移量是相对谁的偏移量有三种选项，三种选项都是定义在`stdio.h`中的常量。
  - `SEEK_SET`:相对起点的偏移量
  - `SEEK_END`:相对终点的偏移量
  - `SEEK_CUR`:相对当前位置的偏移量
- 用于查看当前指针位置的`ftell()`
- 用于重置指针到流的起点的`rewind()`

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151614056.png" alt="image-20220715161426880" style="zoom:67%;" />

## Key Unix I/O Design Concepts

- 一切皆文件
- 使用之前先打开
- 面向字节：一切都是字节流，内核对任何数据或文件的结构和格式一无所知，除了一个特例：目录。
- 内核缓存读和写：
  - 一部分原因是为了缓存和性能
  - 还有一部分原因是硬盘之类的是面向块的，所以一次只能向硬盘写入一块字节。
- 显式关闭

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151631757.png" alt="image-20220715163154598" style="zoom:80%;" />

## Low-Level File I/O: The RAW system-call interface

- `open()`返回的是一个文件描述符，当返回值小于零时出错。
  - Open system call created an *open file description* entry in system-wide table of open files
  - –*Open* *file description* object in the kernel represents an instance of an open file
  - 为什么返回给用户的是一个文件描述符，而不是一个指向已打开文件描述对象的指针？安全，防止用户访问不该访问的东西。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151651393.png" alt="image-20220715165122226" style="zoom:80%;" />

## C Low-Level (pre-opened) Standard Descriptors

- 在`unistd.h`中预定义了三个标准流所对应的文件描述符。
  - 标准输入流：0
  - 标准输出流：1
  - 标准错误流：2
- `FILE`结构体中是记录了文件的文件描述符的，能够通过`fileno()`得到。
- 第三个函数看图

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151656906.png" alt="image-20220715165633769" style="zoom:80%;" />

## Low-Level File API

- 三个都和高等级下的FILE API相似，只不过是有些地方将`FILE*`替换成文件描述符。
- 注：`filedes`:file descriptor.
- `read()`返回读到的字符的个数，不超过`maxsize`指定的个数。返回0代表EOF，-0代表出错。
- `write()`返回写入的字符的个数。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151659892.png" alt="image-20220715165926744" style="zoom:80%;" />

## POSIX I/O: Kernel Buffering

- 读和写都是缓存在内核中的，原因之前已经说过。
  - 内核会一次从磁盘读取一大块的数据缓存起来。

- 其他的以后深入。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151702326.png" alt="image-20220715170245179" style="zoom:67%;" />

## Low-Level I/O: Other Operations

- 最后两个以后深入讲
- Pipe那个用来建立一条管道。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151722243.png" alt="image-20220715172211093" style="zoom: 80%;" />

## High-Level vs. Low-Level File API

- `fread()`并不是传入需要读取的字符个数，就向内核请求多少字符个数，而是会一次请求比如4KB，然后缓存起来，然后从缓存里读。
  - 假如你要读13个字节，`fread()`并不是从内核里请求出13个字节，而是直接带出一大块数据（比如4KB）缓存起来，然后从缓存读取前13个字节。
- 流是被缓存在用户内存中的，而针对文件描述符的操作是立即可见的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151740017.png" alt="image-20220715173959863" style="zoom:67%;" />

## What’s in a FILE?

- FILE结构中有什么：
  - 文件描述符
  - 缓冲区（用户缓冲区，之前提到的）
  - 锁（同步用的，防止多个线程同时访问这个结构）
  - 其他东西不重要。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151742047.png" alt="image-20220715174215905" style="zoom:67%;" />

## FILE Buffering

- 当你借助`FILE`结构使用`fwrite()`向文件中写入数据时，你的数据实际上发生了什么？
  - 数据先被缓存到`FILE`结构体的缓冲区中去。
  - 如果缓冲区满，就刷新缓冲区（把缓冲区现有的内容写入文件）
    - 也就是说你给的数据其实不一定会立即写入文件。
    - 所以不要指望FILE帮你及时写入，而是在有需要的时候人为刷新`fflush()`。
  - C标准库可能会更频繁的刷新缓冲区。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151742950.png" alt="image-20220715174250811" style="zoom:67%;" />



- 例子1：
  - 写入的字符`b`可能已经被写入文件了，也可能只是被缓存在`f1`的缓冲区中，所以`fread()`可能读到`b`也可能没读到。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151745624.png" alt="image-20220715174546474" style="zoom: 80%;" />

- 例子2：人为立即刷新了缓冲区
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151747058.png" alt="image-20220715174709911" style="zoom:80%;" />
- 使用CLION在Windows下可以验证

  - ```c
    #include <stdio.h>
    #include <unistd.h>
    int main() {
        char c = 'x';
        FILE * f1 = fopen("1.txt","w");
        fwrite("b",sizeof(char ), 1, f1);
        //fflush(f1);
        FILE * f2 = fopen("1.txt","r");
        fread(&c, sizeof(char ), 1, f2);
        printf("%c", c);
        fclose(f1);
        fclose(f2);
        return 0;
    }
    ```

    

- Writing Correct Code with FILE：
  - 根据需要加入人为的`fflush()`去刷新缓冲区
  - 调用`fclose()`时会先刷新缓冲区，然后再回收资源。
  - **当使用低等级的file API，就不存在上述问题**（但是也同样没有那么好的性能）
  - `fgetc()`等的性能这么好是因为：你要读的第一个字节会进入内核顺便带回来一大块数据缓存起来，然后后续的读取都直接从缓冲区读取，直到缓冲区耗尽，才会有第二次系统调用（再一次带回一大块数据）。
  - ![image-20220715174817608](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151748755.png)

## Why Buffer in Userspace?

- 在用户空间使用缓存的原因有两个
  - 一是开销，系统调用的开销比函数调用大很多。使用`read()`读取一个字节的开销比使用`fget()`大得多，因为后者会缓存（前面讲过的那种缓存）
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151759515.png" alt="image-20220715175918346" style="zoom:67%;" />
  - 二是为了功能性。比如内核是完全不知道字节流的类型的，它只知道字节流，所以诸如读取一行这样的操作是没办法由内核完成的，因为内核不知道什么是换行符，这种操作就只能在更高层级完成。
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207151800581.png" alt="image-20220715180037425" style="zoom:80%;" />

## State Maintained by the Kernel

- 当成功打开一个文件时，`open()`返回一个文件描述符，同时在内核创建其对应的文件描述对象。
- 对于每个进程，内核维持一个文件描述符到其所对应的打开的文件描述对象的映射。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152038562.png" alt="image-20220715203822384" style="zoom: 80%;" />



- What’s in an Open File Description？
  - 最重要的两个，一个是用来记录在硬盘上的哪个地方找到对应的文件，另外一个是位置指针在文件中的当前位置。

## Abstract Representation of a Process

- 举例：
- 最开始的时候，进程打开一个文件：
  - 文件描述符是3，是因为0、1、2分别是预定义的标准流。
  - Open File Description记录了文件描述符对应的文件，以及当前位置。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152044610.png" alt="image-20220715204407435" style="zoom:67%;" />
- 然后进程往后读了100字节，此时Position变为100。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152046685.png" alt="image-20220715204601512" style="zoom: 67%;" />
- 假如下一步执行`close(3)`，那么变为：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152049517.png" alt="image-20220715204914329" style="zoom:50%;" />
- 假如下一步`fork()`出一个子进程2，**那么子进程2会复制父进程的文件描述符**
  - **并且父子进程共享同一个Open FILE Description**。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152050152.png" alt="image-20220715205042989" style="zoom:67%;" />
- 然后子进程往后读取一百字节，那么Position变为200。
  - **假如父进程要读取一百字节，那么父进程读取的是200-300的那一百字节。**（也就是说Position是共享的）
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152052280.png" alt="image-20220715205231116" style="zoom:67%;" />
- 假如父进程关闭了它的文件描述符，此时对应的文件描述对象（Open File Description）并不会直接被清理：
  - **只会清理掉父进程的指向对应文件描述的指针。**
  - 抓住一点就是父子进程共享`打开文件描述对象(Open file Description)`
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152053322.png" alt="image-20220715205339169" style="zoom:67%;" />

- 对于从同一个终端打开的两个进程来说，他们的三个标准流都会指向同一个终端。
  - 因为这两个进程是终端`fork()`而来的，它们会有同样的文件描述符。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152057325.png" alt="image-20220715205747156" style="zoom: 80%;" />

## Other Syscalls: dup and dup2

- `dup(fd)`：得到一个新的描述符，指向`fd`所对应的打开文件描述，得到的文件描述符不一定就是`fd + 1`。
- `dup2(fd1,fd2)`：指定得到的新的文件描述符为`fd2`

![image-20220715210116397](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152101572.png)

## fork() in Multithreaded Processes

- 概括来说，当在多线程中使用`fork()`时，子进程中永远只有一个线程，那就是调用`fork()`的那个线程。
- 其他线程会直接消失。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152110927.png" alt="image-20220715211054759" style="zoom:80%;" />

## Possible Problems with Multithreaded fork()

- 当在多线程中调用`fork()`时，除了那个调用`fork()`的线程，其他线程都直接消失。这就会带来一些问题。
  - 万一消失的线程有一个正持有锁？
  - 万一消失的线程有一个正处于修改某个数据结构的中途？
  - 总之会有很多乱七八糟的问题。
- **当然如果多线程`fork()`之后子进程直接`exec()`是安全的**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152112438.png" alt="image-20220715211249283" style="zoom:80%;" />

## Conclusion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207152116472.png" alt="image-20220715211636301" style="zoom: 67%;" />

# CS162 Lecture 5: Abstractions 3: IPC, Pipes and Sockets

## Goals for Today: IPC and Sockets

- 进程间的通信机制以及跨越世界的通信是怎么样看起来像文件I/O的。
  - Socket是如何使得位于网络两端的两个进程像读写文件那样借助read()和write()进行通信的。
- 介绍管道和Socket
- TCP/IP

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207192122765.png" alt="image-20220719212155532" style="zoom: 67%;" />

## Why do we need IPC?

- 比如进程之间需要交流以相互协作完成任务，所以需要进程间通信。
- 但是进程这一抽象本身就是不鼓励进程之间的相互通信的，所以我们必须使用特殊的技巧。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201421859.png" alt="image-20220720142103535" style="zoom:67%;" />



## Use a file

- 最简单的实现进程间通信的方法是使用一个文件，一个进程往文件写入，另外一个进程从文件读取。
  - 但是这种方法时间开销很大，因为通过硬盘读写文件的速度是远小于读写内存的。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201431038.png" alt="image-20220720143030778" style="zoom:67%;" />





## Shared Memory: Better Option? Topic for another day!

- 当然一个更好的选择是使用共享的内存，将两个进程的地址空间都映射到同一块内存。
  - 需要锁



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201449160.png" alt="image-20220720144922876" style="zoom:67%;" />

## Communication Between Processes (Another Option)

- 在内存中维持一个队列，**这个队列是使用系统调用才能访问的**
  - 也就是说我们向内核求助，这个内存中的队列应该是由内核来管理的。
  - **由内核管理，使用系统调用是处于安全原因**
  - **需要和前面提到的共享内存区分开，在这个方法之下，队列是处于内核的管理之下的，而前面提到的共享内存是在用户态的**
- 进程A向内存中的队列`write()`，进程B从内存中的队列`read()`。
  - A产生的数据会保存在内存中，直到B读取它。
  - 这样比使用文件要更高效因为我们不会访问硬盘。
  - 所使用的接口和文件读写是一样的。
- 但是还有一些问题：
  - 怎么建立这个连接？
  - 如果生产者（A）生产数据的速率高于B能够消耗的速率呢？
    - 这样队列会满，所以在队列满时，如果A调用了`write()`，A就要陷入休眠，直到队列中有足够的空间为止。
    - 当队列变空时要唤醒Ａ，完成写入。
    - 当然，假定B永远不读取数据，A不可能永远等待下去，所以需要设置一定的超时值。
  - 如果生产者A的生产速率低于B消耗的速率？
    - 这样队列会空，所以在队列空时，如果B调用了`read()`，B就要陷入休眠，直到队列中变为不空。
    - 当队列变为非空时要唤醒B，完成读取。
    - 如果Ａ永远不写入新的数据，B也不可能一直等待下去，所以也需要一个超时值。
  - 但是不管怎么样，两个进程之间不会有过大的危害，也不会有死锁。
- **于是这就引出了我们的pipe**，管道就是一个内存中的队列，并且也是缓冲在内核中的。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201454951.png" alt="image-20220720145428666" style="zoom:67%;" />

### One example of this pattern: POSIX/Unix PIPE

- 系统调用`pipe(int fileds[2])`，参数是一个`int`型数组，这个数组会被`pipe()`填充好两个文件描述符，两个文件描述符分别对应管道的读端和写端。
  - **fileds[0]是读端，fileds[1]是写端**
  - 当生产者试图在缓冲区满时写入，它陷入阻塞（休眠直到缓冲区有空间）
  - 当消费者试图在缓冲区空时读取，它陷入阻塞（休眠直到缓冲区有数据）
  - 缓冲区（内存中的队列，位于内核）是固定尺寸的。
  - **问题：生产者陷入休眠以后是怎么队列空的，消费者陷入休眠以后是怎么知道队列有新的数据的**
    - 换句话说，我们怎么知道管道里有没有数据的？难道要时不时去检查？
    - 内核知道，当有新的数据的到来时通知休眠的消费者，有空间。内核全都知道。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201516449.png" alt="image-20220720151625157" style="zoom:67%;" />



- 栗子1，使用管道在进程内部通信：

  - 从管道的两端的读和写都是调用文件读写的系统调用`read()`和`write()`.

  - **在通信结束后要关闭两个文件描述符**。

  - 这个例子在管道的写入端写入后立即在管道的读取端读取。

  - ```c
    #include <unistd.h>
    int main(int argc, char *argv[])
    {
      char *msg = "Message in a pipe.\n";
      char buf[BUFSIZE];
      int pipe_fd[2];
      if (pipe(pipe_fd) == -1) {
        fprintf (stderr, "Pipe failed.\n"); return EXIT_FAILURE;
      }
      ssize_t writelen = write(pipe_fd[1], msg, strlen(msg)+1);
      printf("Sent: %s [%ld, %ld]\n", msg, strlen(msg)+1, writelen);
    
      ssize_t readlen  = read(pipe_fd[0], buf, BUFSIZE);
      printf("Rcvd: %s [%ld]\n", msg, readlen);
    
      close(pipe_fd[0]);
      close(pipe_fd[1]);
    }
    
    ```

    

- 栗子2，进程间的管道通信：

  - 先打开管道，然后使用`fork()`生成子进程，然后两个进程会共享文件描述符，像下图那样

    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201545616.png" alt="image-20220720154530290" style="zoom: 50%;" />

  - 然后可以选择在父进程关闭写入端的文件描述符，在子进程关闭读取端的文件描述符，就得到了一个子进程写入，父进程读取的单向管道。当然，也可以反过来，就长下面那样。

    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201546060.png" alt="image-20220720154652754" style="zoom: 50%;" />

  - ```c
    #include <unistd.h>
    int main(int argc, char *argv[])
    {
      char *msg = "Message in a pipe.\n";
      char buf[BUFSIZE];
      int pipe_fd[2];
      if (pipe(pipe_fd) == -1) {
        fprintf (stderr, "Pipe failed.\n"); return EXIT_FAILURE;
      }
      pid_t pid = fork();
      if (pid < 0) {
        fprintf (stderr, "Fork failed.\n");
        return EXIT_FAILURE;
      }
      if (pid != 0) {
        ssize_t writelen = write(pipe_fd[1], msg, msglen);
        printf("Parent: %s [%ld, %ld]\n", msg, msglen, writelen);
        close(pipe_fd[0]);
      } else {
        ssize_t readlen  = read(pipe_fd[0], buf, BUFSIZE);
        printf("Child Rcvd: %s [%ld]\n", msg, readlen);
        close(pipe_fd[1]);
    }
    ```

## When do we get EOF on a pipe?

- 当最后一个写入端的文件描述符被关闭之后，再从管道读取数据，就只能读取到EOF，像下面那样。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201600355.png" alt="image-20220720160035048" style="zoom: 67%;" />

- 在最后一个读取端的文件描述符被关闭之后，再向管道写入数据就会生成一个`SIGPIPE(sig-pipe)`信号。
  - 如果进程忽略这个信号，这个写入会失败，并且出现`EPIPE(e-pipe)`错误

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207201558103.png" alt="image-20220720155800799" style="zoom:67%;" />



## What is a Network Connection?

- 两个处于不同机器上的进程之间的双向的字节流
  - 目前只讨论TCP
- 一个处于两个端点A和端点B之间的连接由：
  - 从A到B的用于数据传输的队列（有限缓冲区）
  - 从B到A的用于数据传输的队列（有缓冲区）构成。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211119287.png" alt="image-20220721111940045" style="zoom:67%;" />

## The Socket Abstraction: Endpoint for Communication

- 中心思想：跨越网络的进程间通信也像文件读写（有`read()`和`write()`）
- Socket是通信的两个端点，每个Socket都有缓冲区用来暂存数据。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211125495.png" alt="image-20220721112510294" style="zoom:67%;" />

### Sockets: More Details

- Socket是另外一种实现进程间通信的机制
- Socket几乎出现在所有类型的操作系统上，分布及其广泛。
- Socket在BSD Unix4.2上被引入
- 在任意种类的网络上Socket的抽象都是一致的

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211128563.png" alt="image-20220721112854366" style="zoom:67%;" />

- Socket看上去就和带有文件描述符的文件是一样的，`write()`写入，`read()`读出
- **有一些文件操作不适用于Socket，比如调整字节流指针的`lseek()`**
  - 和调整文件字节流指针的操作都没有，因为Socket总是在尾部加入新数据，另外一端尾部读取。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211132637.png" alt="image-20220721113219462" style="zoom:67%;" />

### Socket Creation

- 文件系统在一个结构化的命名空间内提供了永久的对象的集合
  - 进程能够打开、读写、关闭文件
  - 文件的存在独立于进程
  - 能够通过指定文件名地方式轻易打开文件(`open()`)
- 管道：为处于同一台物理机器上的两个进程之间的单向的通信。
  - 单个的队列
  - 通过系统调用`pipe()`创建的暂时性的东西
  - 文件描述符是从父进程传递给子进程的
  - **不是一个全双工的通道，因为如果两个进程同时借助写入端的文件描述符写入的话，写入的字节流会互相穿插在一起。**
- 而Socket：双向的，两个进程之间的通信，这两个进程可能不在同一台机器上
  - 两个队列，每个方向上一个，分别处于两端的Socket
  - 第一个问题：使用IP来解决命名空间的问题
  - 第二个问题：使用端口号来定位要通信的进程

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211547477.png" alt="image-20220721154756257" style="zoom:67%;" />

### Connection Setup over TCP/IP

- 一个CS架构的模型
- 服务器端的`sever socket`负责监听和接收来自客户端的连接，**但并不负责处理连接建立后的事务**
  - 每当`sever socket`accept()之后，一个新的socket会被创建出来处理这个连接。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211555345.png" alt="image-20220721155511161" style="zoom:67%;" />

#### Client Protocol

```c
char *host_name, *port_name;

// Create a socket
struct addrinfo *server = lookup_host(host_name, port_name);
int sock_fd = socket(server->ai_family, server->ai_socktype,
                     server->ai_protocol);

// Connect to specified host and port
connect(sock_fd, server->ai_addr, server->ai_addrlen);

// Carry out Client-Server protocol
run_client(sock_fd);

/* Clean up on termination */
close(sock_fd);

```



#### Server Protocol (v1)

- 缺点：
  - 一次只能处理一个连接，当当前连接没有处理完时，其他连接没办法处理
  - 服务器进程没办法保护自己

```c
// Create socket to listen for client connections
char *port_name;
struct addrinfo *server = setup_address(port_name);
int server_socket = socket(server->ai_family,	server->ai_socktype, server->ai_protocol);
// Bind socket to specific port
bind(server_socket, server->ai_addr, server->ai_addrlen);
// Start listening for new client connections
listen(server_socket, MAX_QUEUE);

while (1) {
  // Accept a new client connection, obtaining a new socket
  int conn_socket = accept(server_socket, NULL, NULL);
  serve_client(conn_socket);
  close(conn_socket);
}
close(server_socket);

```

#### Server Protocol (v2)，Sockets With Protection (each connection has own process)

- 改进：
  - 每当`accept()`一个连接之后，`fork()`一个子进程出来，由子进程来处理连接后的事务。
  - 父进程则`wait()`直到子进程结束。
  - 由于子进程不需要`sever socket`（不需要接受新的连接），所以子进程会关闭自己对应的文件描述符。
  - 由于父进程不需要去处理连接后的事务（不需要服务客户端），所以父进程会关闭自己对应的文件描述符。
- 缺点：并发性要求没有得到满足
  - A concurrent server can handle and service a new connection before the previous client disconnects

```c
char *port_name;
struct addrinfo *server = setup_address(port_name);
int server_socket = socket(server->ai_family,	server->ai_socktype, server->ai_protocol);
// Bind socket to specific port
bind(server_socket, server->ai_addr, server->ai_addrlen);
// Start listening for new client connections
listen(server_socket, MAX_QUEUE);
while (1) {
  // Accept a new client connection, obtaining a new socket
  int conn_socket = accept(server_socket, NULL, NULL);
  pid_t pid = fork();
  if (pid == 0) {
    close(server_socket);
    serve_client(conn_socket);
    close(conn_socket);
    exit(0);
  } else {
    close(conn_socket);
    wait(NULL);
  }
}
close(server_socket);
```

#### Server Protocol (v3)

```c
// Socket setup code elided…
while (1) {
  // Accept a new client connection, obtaining a new socket
  int conn_socket = accept(server_socket, NULL, NULL);
  pid_t pid = fork();
  if (pid == 0) {
    close(server_socket);
    serve_client(conn_socket);
    close(conn_socket);
    exit(0);
  } else {
    close(conn_socket);
    //wait(NULL);
  }
}
close(server_socket);
```

- 改进：父进程不再`wait()`子进程结束，而是直接进入下一个循环来接收新的连接。
- 缺点：使用子进程的开销太大了，可以改为使用多线程。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211608959.png" alt="image-20220721160848758" style="zoom: 67%;" />
- 使用多线程会带来另外一个问题：
  - 服务器可能会在短时间内接收高并发的访问，导致线程数量高速增长，最终导致内核崩溃。
  - 解决方法：使用线程池，限制线程的数量。
  - 每当来一个新的连接请求，将其插入队列等待，当有一个线程空闲，出队下一个连接。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211612097.png" alt="image-20220721161247885" style="zoom:67%;" />

## Conclusion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211614738.png" alt="image-20220721161454443" style="zoom:67%;" />

# CS162: Lecture 6: Synchronization 1: Concurrency and Mutual Exclusion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211910978.png" alt="image-20220721191006770" style="zoom:67%;" />

## Goals for Today: Synchronization

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211922208.png" alt="image-20220721192205983" style="zoom:67%;" />

## Multiplexing Processes: The Process Control Block

- 复习了一下PCB

- 内核使用PCB来代表每个进程，PCB中存储了这个进程的：

  - 状态（运行中、阻塞、就绪，etc）
  - 寄存器状态
  - PID、用户、可执行文件、优先级等
  - 启动时间
  - 内存空间、地址翻译等等

  

- 略微讲了一下内核调度器。
  - 调度器维护了一个数据结构，这个数据结构包含了每个进程的PCB，调度器负责调度CPU资源和非CPU资源。

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211935990.png" alt="image-20220721193505768" style="zoom:67%;" />

## Context Switch

- 上下文切换：
  - 保存进程状态到PCB
  - 从另外一个PCB加载另外一个进程的状态
  - 运行另外一个进程
  - 另外一个进程再上下文切换
  - **过于频繁的上下文切换会导致程序实际运行的时间占比很小，而上下文切换的开销占比极大**
  - 进程运行在用户态，而上下文切换（保存和恢复PCB等等）是属于内核态（红框）。
  - x86实际上有四种权限，但是当做是两种就行了。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207211949598.png" alt="image-20220721194944387" style="zoom:67%;" />

## Lifecycle of a Process or Thread

- 适用于线程和进程。
- 直接以进程为例来说明它们的生命周期：
  - 新建：进程刚被创建出来的时候
  - 就绪：进程可以运行，但还没有运行，当一个进程被创建出来，插入就绪队列之后，就是就绪。
  - 运行：进程正在占用CPU
  - 等待：由于I/O或者某些事件，进程进入阻塞
  - 终止：进程执行完毕，但是还没有被释放掉（僵尸进程状态，已经终止但是没有被再分配）
    - 为什么？因为父进程需要得到子进程的结果或者信息什么的。比如返回值什么的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212000668.png" alt="image-20220721200012449" style="zoom:67%;" />

## Scheduling: All About Queues

- PCB总是从一个队列移到另外一个队列

- 一个PCB从就绪队列移到CPU，然后可能因为I/O移到I/O队列再回到就绪队列，也有可能因为时间片到期而回到就绪队列，等等原因。
- **当启用fork()得到子进程时，不能想当然认为子进程会进入就绪队列或者是父进程会进入就绪队列，这个和调度策略有关，永远不能假设哪个先运行**
- 目前所说的调度指的仅仅是决定就绪队列中谁是下一个得到CPU时间的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212006571.png" alt="image-20220721200623344" style="zoom:67%;" />

## Ready Queue And Various I/O Device Queues

- 每个队列都代表了一系列的被暂时挂起的进程。
- 每个队列实际上是链表，调度器只与就绪队列交互，其他的队列通过设备驱动来交互

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212039467.png" alt="image-20220721203919243" style="zoom:67%;" />

## The Core of Concurrency: the Dispatch Loop

- 首先复习一下线程相关：
  - 为什么多线程一个地址空间？为了共享
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212057185.png" alt="image-20220721205700970" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212057720.png" alt="image-20220721205750487" style="zoom:67%;" />
- 进入正题：
  - 从概念上来说，其实调度就是一个死循环：
    1. 运行线程
    2. 选定下一个线程
    3. 保存当前线程的状态
    4. 加载新线程的状态
    5. 回到1。
  - 甚至可以说这就是操作系统的全部工作。只有在机器关机或者机器崩溃的时候退出这个循环。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212103406.png" alt="image-20220721210341189" style="zoom:67%;" />

## Running a thread

- 运行一个线程很简单，保存旧状态，加载新状态，加载运行时环境，跳转到PC。
- 但是有个问题，假如只有一个CPU的情况下，OS和其他线程都是运行在这个CPU上的，当OS运行的时候其他线程就不运行，其他线程运行的时候OS就不能运行，这意味着OS在其他线程运行的时候是给出了CPU的控制权的，如果应用程序陷入死循环，OS需要想办法把控制权拿回来。
- 调度器如何把控制权拿回来？
  - 内部事件：（当每个线程都协作得很好，没有bug时）线程自愿返回控制权
  - 外部事件：线程被抢占

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212117448.png" alt="image-20220721211750135" style="zoom:67%;" />

### Internal Events

- I/O阻塞，等待硬盘读写时线程可以让出CPU
- 等待其他线程的信号，此时线程可以让出CPU
- 使用系统调用`yield()`自愿让出CPU
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207212146593.png" alt="image-20220721214656367" style="zoom:67%;" />
- 新加入两个POSIX API:自愿让出CPU的`yield()`
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221342639.png" alt="image-20220722134216354" style="zoom:67%;" />

### Stack for Yielding Thread

- 蓝色部分是用户栈，红色部分是内核栈
  - 对于每一个线程（每一个用户栈），都有一个与之一一对应的内核栈。
  - 为什么使用内核栈而不是直接使用用户栈？安全，内核不相信用户。

- 运行一个新线程的过程：
  - 选择下一个要运行的线程
  - 做好上下文切换工作
    - 保存现有线程状态，加载下一个线程的状态
  - 做好后续清理和善后工作（比如资源回收）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221345259.png" alt="image-20220722134550029" style="zoom:67%;" />

### What Do the Stacks Look Like?

- 两个假设：
  - 只有两个线程S和T，最开始是线程S在运行。
  - 两个线程S和T的源代码是一样的。
- 最开始，线程S调用A函数，A调用B，B调用`yield()`。
  - `yield()`陷入内核栈，调用`run_new_thread()`
  - `run_new_thread()`调用`switch()`
  - **`switch()`执行完毕后从线程T的调用栈返回到`yield()`，`yield()`返回B**。
  - 然后线程T的B再调用`yield()`，然后调用`run_new_thread()`，然后调用`switch()`，然后从线程S的调用栈返回，如此循环往复。
- 为什么在线程S调用`switch()`之后，栈是先从线程T的底部返回，然后到达顶部后再自顶向下？
  - 因为`switch()`执行完毕后是需要执行`ret`指令的，而`switch()`本身在执行的过程中是会更改栈指针的（将`%rsp`的值由S的栈顶指针改为T的栈顶指针），也就是说`switch()`在执行完毕后，地址空间内实际上现存的栈是线程T的栈，而`ret`指令是从栈里`pop()`出一些东西，这个操作最终实际上是在线程T的栈上执行的。
- **在执行`switch()`之后，线程相对应的内核栈也是进行了切换的，保持用户栈和内核栈的一一对应的关系。**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221500295.png" alt="image-20220722150008045" style="zoom:67%;" />

### Switch Details (continued)

- `switch()` 的伪代码（伪汇编代码？）。
  - 大概就是保存现有寄存器值到TCB，加载新TCB的值到CPU
  - `switch()`执行完毕后会返回的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221530494.png" alt="image-20220722153033265" style="zoom:67%;" />
- 如果在实现`switch()`的时候出错了会怎么样？
  - 可能会出错，也可能不会，取决于你犯错的那个地方有没有被用到
- 能否完全全面的测试`switch()`的代码？不可以。
- 最后一点是小故事，不用看
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221531069.png" alt="image-20220722153150845" style="zoom:67%;" />

### Aren't we still switching contexts?

- 线程间的上下文切换要比进程间的上下文切换开销要小得多，因为不需要改变地址空间。
- 甚至可以让开销更小：
  - 典型的情况下，一个用户栈有一个与之对应的内核栈，在进行上下文切换的时候需要进入内核
  - 但是我们可以使用多个线程对应一个内核线程的情况，这种情况下上下文切换的开销更加小（不需要进入内核），但是代价是如果其中一个用户线程进入内核线程，被阻塞，其余用户线程也会被挂起。
  - 所以有了多对多模型。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221613116.png" alt="image-20220722161348884" style="zoom:67%;" />

### Processes vs. Threads

- **Concurrency** is when two or more tasks can start, run, and complete in overlapping time **periods**. It doesn't necessarily mean they'll ever both be running **at the same instant**. For example, *multitasking* on a single-core machine.
- **Parallelism** is when tasks *literally* run at the same time, e.g., on a multicore processor.

- No Parallelism，ONLY concurrency

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221620435.png" alt="image-20220722162043196" style="zoom:67%;" />

- Parallelism

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221620374.png" alt="image-20220722162055152" style="zoom:67%;" />

### Simultaneous MultiThreading/Hyperthreading

- 超线程：大概就是一个核上同时运行两个线程？使用流水线

### External Events

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221711245.png" alt="image-20220722171159004" style="zoom:67%;" />

- 外部事件用于保证即使程序出现了bug，调度器也可以成功地取回控制权。
  - 使用中断或者定时器来确保。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221715262.png" style="zoom:67%;" />

### How do we initialize TCB and Stack?

- 初始化TCB的寄存器域：
  - 栈指针指向栈
  - PC返回地址置为OS的程序`ThreadRoot()`
  - 两个参数寄存器被初始化为函数指针和函数参数指针。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221729597.png" alt="image-20220722172902380" style="zoom:67%;" />



## How does Thread get started?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221731785.png" alt="image-20220722173116567" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221732192.png" alt="image-20220722173200968" style="zoom:67%;" />

## Correctness with Concurrent Threads?



### Atomic Operations

- 原子操作：总是运行到完成或者干脆一点都不运行的操作。
  - 无法在运行的中途被停下来，也无法在中途改变其状态
  - 如果没有原子操作，无法完成多线程同步
  - 目前只有load和store是原子操作。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221801063.png" alt="image-20220722180118799" style="zoom:67%;" />

- 复习了一下锁：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221805527.png" alt="image-20220722180536300" style="zoom:67%;" />
- 复习了几个和同步相关的定义：
  - 同步：使用原子操作来保证线程之间的协作
  - 互斥：保证一次只有一个线程能做某件特定的事
  - 临界区：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207221806586.png" alt="image-20220722180601356" style="zoom:67%;" />

# CS162: Lecture 6.5: Concurrency and Mutual Exclusion (Supplemental)

# CS162 Lecture 7: Synchronization 2: Semaphores (Con't), Lock Implementation, Atomic Instructions



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231315083.png" alt="image-20220723131501765" style="zoom:67%;" />

## Producer-Consumer with a Bounded Buffer

- 生产者消费者模型：
  - 有多个生产者和多个消费者，生产者将东西放入缓冲区，消费者从缓冲区取东西
  - 缓冲区是有限容量的。
  - 放东西和取东西都是随时可能发生的
  - 当缓冲区满时，生产者要放东西，就让他休眠
  - 当缓冲区空时，消费者要取东西，也让他休眠。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231601131.png" alt="image-20220723160119866" style="zoom: 67%;" />

## Circular Buffer Data Structure (sequential case)

- 我们的缓冲区对象的结构：
  - 两个index，一个是写入index，一个是读取index，这个index是可以回绕的（也就类似ArrayList）。
  - 一个存放东西的缓冲区`entries`
  - 可以看做是一个有两头index的环形数组（`ArrayList<T>`），写入就是入队，读取就是出队。
  - 存入东西就是写入，然后把`write_index`修改一下，读取类似。
- 现在需要做的就是保证对这个缓冲区的操作都是原子性的。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231602330.png" alt="image-20220723160252112" style="zoom:67%;" />

## Circular Buffer – first cut

- 基本思想就是先加锁，假如缓冲区空或者满，消费者或者生产者就使用空`while()`等待直到缓冲区不空或者不满，然后再做操作，最后释放锁。
- **但是这样的思路有两个问题**：
  - 对于生产者来说，缓冲区永远不会有空位。因为生产者在`while()`循环等待缓冲区变空的时候，它是给缓冲区加了锁的，此时其他的消费者是没办法访问缓冲区的，所以缓冲区也永远不会变得有空位，生产者也永远跳不出`while()`循环。
  - 对于消费者来说，缓冲区永远不会有东西。原因与生产者类似。
  - 上述问题引出了一个新问题，那就是关于那个`while()`循环浪费CPU时间，等待的时候完全可以休眠而不是`while()`忙等

```c
mutex buf_lock = <initially unlocked>

Producer(item) {
  acquire(&buf_lock);//加锁
  while (buffer full) {}; // Wait for a free slot，假如缓冲区满，就一直在循环结构空转，直到缓冲区不满，跳出循环
  enqueue(item);//缓冲区有空位，插入
  release(&buf_lock);//释放锁
}
//类似
Consumer() {
  acquire(&buf_lock);
  while (buffer empty) {}; // Wait for arrival
  item = dequeue();
  release(&buf_lock);
  return item
}

```

## Circular Buffer – 2nd cut

- 第二版好一点，**至少能用了**。
- 但是除了能用，缺点也很明显：
  - 还是忙等问题。假如缓冲区是满的，并且长时间都没有消费者来取东西，那么生产者就一直处于`释放锁-拿锁` 循环中，一直在浪费CPU的时间，但是什么都没做，这部分时间本来是可以让出去的。
  - 对于消费者来说也存在类似的问题。

```c
mutex buf_lock = <initially unlocked>

Producer(item) {
  acquire(&buf_lock);
  while (buffer full) {release(&buf_lock); acquire(&buf_lock);} 
  enqueue(item);
  release(&buf_lock);
}

Consumer() {
  acquire(&buf_lock);
  while (buffer empty) {release(&buf_lock); acquire(&buf_lock);} 
  item = dequeue();
  release(&buf_lock);
  return item
}

```

## Recall: Semaphores

- 信号量，最初由Dijkstra定义。
- 定义：一个信号量有一个非负的整数值，并且支持以下两个操作：
  - Dwon()，或者说P()，一个原子操作，等待信号量变为正数，然后将其减一（如果信号量本来就是正的，不需要等待）
  - Up()，或者说V()，一个原子操作，将信号量的值加一，然后唤醒正在等待的P()，如果有的话。
  - P和V都是Dutch。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231721989.png" alt="image-20220723172134698" style="zoom:67%;" />

### Semaphores Like Integers Except…

- 信号量很像整数，但是：
  - 是非负的整数
  - 只支持P和V运算，而不支持读写值，除了初始化的时候
  - 运算必须是原子化的
    - 两个P()一起，不能将信号量减至零以下。
    - 调用P进入休眠的线程不会错失来自V的唤醒，即便两个操作同时发生
  - POSIX支持查看值，但是。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231725639.png" alt="image-20220723172527415" style="zoom:67%;" />

### Two Uses of Semaphores

- 信号量的两个应用

- 互斥锁：也叫二进制信号量，互斥锁
  - 初始值为1。
  - 能够应用于实现互斥，就是一个锁。
  - 进入临界区时执行`P()`(将值减为0，如果当前值为0就等待)
  - 出临界区时执行`V()`。
- 调度约束：
  - 允许线程1等待来自线程2的信号，线程2会在给定事件出现时调度线程1。
  - 比如`ThreadJoin()`:
    - 初始值为0
    - 父进程等待子进程结束，父进程执行`P()`，由于初始值为0，父进程陷入等待。
    - 子进程结束的时候执行`V()`，将信号量置为1同时唤醒父进程。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231731778.png" alt="image-20220723173143519" style="zoom:67%;" />

## Revisit Bounded Buffer: Correctness constraints for solution

- 给出缓冲区保证正确性的多个限制条件，以及完整的解决方法。
- 限制条件：
  - 如果缓冲区完全空，消费者必须等待生产者填充缓冲区
  - 如果缓冲区完全满，生产者必须等待消费者腾空缓冲区
  - 一次只能有一个线程能够操纵缓冲区
- **对于每个限制条件，应该使用一个单独的信号量**。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231747112.png" alt="image-20220723174659876" style="zoom:67%;" />

### Full Solution to Bounded Buffer (coke machine)

- 三个限制，所以三个信号量。
- `fullSlot`记录填充了多少格子
- `emptySlot`记录还有多少个空格子
- `mutex`是信号量，作为互斥锁
- 生产者：
  - 先检查是否还有空格子（对`emptySlot`执行`P()`，如果`emptySlot`等于0，陷入等待）
  - 被来自消费者的`V()`唤醒之后加锁，然后插入东西
  - 插入完毕之后放锁，然后执行`V()`操作将`fullSlot`加一，同时给消费者发出信号，告知消费者缓冲区有东西可以取
- 消费者：
  - 先检缓冲区是否为空（对`fullSlot`执行`P()`，如果`fullSlot`等于0，陷入等待）
  - 被来自生产者的`V()`唤醒之后，加锁，然后拿走一个东西。
  - 插入完毕之后放锁，然后对`emptySlot`执行`V()`将其加一，同时给生产者发出信号，唤醒生产者等待空格的`P()`
- 生产者的`semaV(&fullSlots)`唤醒的是消费者的`semaP(&fullSlots)`，意思就是生产者唤醒消费者告诉它有东西可以取了
- 消费者的`semaV(&emptySlots)`唤醒的是生产者的`semaP(&emptySlots)`，意思是消费者唤醒生产者告知它有空格子可以放东西了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207231820674.png" alt="image-20220723182005414" style="zoom:67%;" />

### Discussion about Solution

- 为什么不对称？废话
- P的顺序重要吗？很重要，不然会导致死锁。
  - 生产者如果先获取互斥锁，然后再检查是否还有空位，当没有空位的时候就会死锁，因为消费者没办法在拿不到锁的情况下给缓冲区腾出空间。
- V的顺序（也就是是先释放互斥锁还是先修改格子的） 不重要，就是可能会影响调度效率。
  - 以生产者为例，先释放锁，即使没来得及修改格子的数量（由0到1）就调度到了消费者，消费者也会由于不满足条件（此时fullSlot还是0，缓冲区看上去还是空的）而再一次陷入等待，然后调度回生产者。更大的可能是，消费者由于此前发现不满足条件陷入了沉睡，现在还在沉睡中等待生产者的V唤醒。


<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207232032263.png" alt="image-20220723203201989" style="zoom:67%;" />

## Motivating Example: “Too Much Milk”

- 背景大概就是，A和B是舍友，二者共用一个冰箱，二者都需要喝牛奶，如果冰箱里没有牛奶，二者都会去买牛奶。
- 表格里是没有同步的时候会发生的情况：A先发现没有牛奶，去买牛奶了，然后B才发现没有牛奶，但是此时A还没把牛奶买回来，于是二者都去买牛奶了，于是冰箱里就会有多余的牛奶。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241208604.png" alt="image-20220724120800270" style="zoom:67%;" />

### Too Much Milk: Correctness Properties

- 为了保证冰箱里不会有太多的牛奶，我们做出如下的限制：
  - 永远不超过一个人去买牛奶
  - 如果没有牛奶，要有人去买。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241211748.png" alt="image-20220724121102512" style="zoom:67%;" />

### Too Much Milk: Solution #1

- 去买牛奶的人在出发之前留下一张便条表示自己去买了。
  - 先留下便条，说自己买牛奶去了
  - 然后买牛奶
  - 买回来之后把便条去掉。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241211851.png" alt="image-20220724121152610" style="zoom:67%;" />

- 问题：
  - 假如出现以下情况：线程A和B代表两个人
  - 线程A检查到没有牛奶，没来得及检查是否有便条就被调度走。
  - 调度到线程B，线程B也检查到没有牛奶，同时检查到没有便条，然后没来得及留便条就被调度走。
  - 调度回线程A，A检查到没有便条，于是出门把牛奶买回来。
  - 然后调度回到B，B由于已经通过了`If()` 检查，也去买牛奶。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241226598.png" alt="image-20220724122635346" style="zoom:67%;" />
- 结果：
  - 偶尔仍然会出现牛奶过量的情况，因为线程可能会在恰好通过检查且没来得及放便条的时候被调度走。
  - 这种方法并没有改善问题，而是让问题更加难以被解决，因为出现更加少，更加难以复现和debug。
  - **我们的解决方法应该保证不管调度器如何调度都不会出错**
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241232872.png" alt="image-20220724123219649" style="zoom:67%;" />

### Too Much Milk: Solution #1½ 

- 既然会来不及放便条，那轮到我的时候我第一件事就是放便条？
- 直接死锁，因为先放了便条，然后再去检查是否有便条，这样永远都有便条，没有任何人能够去买牛奶。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241238261.png" alt="image-20220724123857018" style="zoom:67%;" />

### Too Much Milk Solution #2

- 第三种方法也不行。考虑下列情况：非常隐秘，也极度不可能发生，但是在最坏的时间可能发生。
  - A先留下A的便条，然后被调度走。
  - 调度到B，B留下便条，然后被调度走。
  - 调度回A，此时AB都留下了便条，A不能去买牛奶。
  - 调度到B，B也不能去买牛奶，因为A留下了便条。
  - 于是大伙都喝不到牛奶。
- 这种情况叫做饥饿锁。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241349817.png" alt="image-20220724134907540" style="zoom:67%;" />

### Too Much Milk Solution #3

- 能够解决牛奶过多的问题，但是存在一些缺点。
  - 可以穷举每一种情况，都是能够解决问题的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241359262.png" alt="image-20220724135900997" style="zoom: 67%;" />
- 缺点：
  - 最主要的缺点就是线程A在有noteB的时候在忙等（wasting cycles）
  - 代码非常复杂，但是解决的问题却很简单
  - A和B的代码是不一样的
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241404805.png" alt="image-20220724140431562" style="zoom:67%;" />

### Too Much Milk: Solution #4?

- 加锁
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241408270.png" alt="image-20220724140809016" style="zoom:67%;" />

## Back to: How to Implement Locks?

- 如果使用原子的Load和Store指令来实现锁，会很复杂而且容易出错
- 使用硬件锁指令？
  - 复杂度很高，并且不好实现线程的休眠。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241413111.png" alt="image-20220724141300793" style="zoom:67%;" />

### Naïve use of Interrupt Enable/Disable

- 直接把开关中断当做是一个锁。

  - 因为把中断关了，外部事件就无法夺取线程的控制权了
  - 只要保证不出现内部事件，那么就不会出现线程被调度走的情况（保证了线程可以执行完临界区的代码而不被打断，也就是一次只有一个线程进入临界区）。
  - 也就等于是变相加锁。

- 因此，加锁的实现就是把中断给禁用了，放锁的实现就是把中断重新启用。

- 这个方法的问题：

  - **绝对不能让用户以这样的方式加锁**：因为加了锁之后就没有中断了，那么假如用户陷入死循环，没有办法把CPU的控制权拿回来。

    - ```c
      LockAcquire();//加锁，关闭了中断，外部事件没办法夺取控制权
      while(1){
          ;
      }//一直死循环，没办法放锁，中断没办法重新启用
      ```

  - 对于实时系统来说，没有办法满足实时性的要求，这可能是很大的安全问题。

    - 比如汽车的系统，对延迟的要求很高，实时性是安全性的保证。

  - 对于I/O这样的事件无法及时响应

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241418176.png" alt="image-20220724141825852" style="zoom:67%;" />

### Better Implementation of Locks by Disabling Interrupts

- 基本思想：使用一个锁变量，而不是使用中断的开关本身来作为锁。只有在加锁和放锁的过程中关中断。
- 加锁：
  - 关中断，检查锁变量是否是BUSY。
  - 如果不是，将其置为BUSY，拿到锁，开中断。
  - 如果是，将线程放入等待队列，陷入休眠，但是不开中断（为什么？带着关中断休眠？）。**休眠结束后开中断。**
- 放锁：
  - 关中断，检查等待这个锁的等待队列中是否有其他线程在等待这个锁。
  - 如果有，将这个线程放入就绪队列（唤醒其休眠），但是不修改锁变量为FREE，保留其为BUSY。（为什么？）
    - 因为其实没有必要先把`value`修改为FREE，然后再由拿到锁的线程修改为BUSY。保留`value`本身为BUSY，同时将线程放入就绪队列，实际上就是将锁给它了。其他要锁的线程是要不到锁的，因为检查到`value`是BUSY。
  - 如果没有，修改锁变量的值为FREE。
  - 不管有没有，最终都要开中断。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241430614.png" alt="image-20220724143042328" style="zoom:67%;" />

- 为什么需要关中断？
  - 检查和修改锁变量`value` 值相当于临界区，开关中断相当于在给临界区加锁。
  - 开中断是为了保证在检查和修改`value`的值的时候不会被调度走，防止同时多个线程拿锁。
  - 这里开关中断包括的临界区很短，中断被禁用的时间不长，关键中断能及时响应。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241439483.png" alt="image-20220724143955190" style="zoom:67%;" />
- 为什么在加锁的时候，没拿到锁去休眠，不把中断重新启用？
  - 把线程放到等待队列之前开中断：
    - 线程发现锁是BUSY，开中断，准备把线程放到锁的等待队列上，然后调度器调度。
    - 新的线程将锁释放，并且发现等待队列上没有线程等待，直接将锁置为FREE。
    - 回到旧线程，就线程将自己放入队列，进入休眠，但是这时候，锁却是FREE的。
  - `GotoSleep()`之前开中断：
    - 线程这次把自己放入等待队列了，然后开中断，调度器调度。
    - 新的线程释放锁，并且发现等待队列上旧线程在等待，于是把其放入调度器的就绪队列。
    - 旧的线程从就绪队列进入运行状态，然后下一步运行的是`sleep()`。
    - 情况变成了，旧的线程拿到了锁，但是它陷入了休眠，成了死锁。
  - `GotoSleep()`之后开中断？怎么开？这个线程本身休眠了，肯定没办法让它负责开
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241502903.png" alt="image-20220724150248578" style="zoom:67%;" />
- `GotoSleep()`之后开中断？
  - 线程A关中断==>上下文切换==>线程B开中断,运行一段时间==>关中断>上下文切换==>A开中断。
  - 意思就是说，由上下文切换之后的新线程负责开中断。这本身也是很合理的，因为上下文切换的时候本身就是需要关中断的，保存和加载状态的时候是不能被中断打断的。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207241528139.png" alt="image-20220724152819824" style="zoom:67%;" />
  
- 问题：

  - 没办法把这个锁的实现放在用户态
  - 对于多核处理器来说不是很适用，因为要关多个核的中断很耗时间。
  - 替代：原子指令序列。
    - 这些指令序列的读和写都是原子性的，这个特性由硬件负责实现。
    - 能适用于单核和多核处理器。

  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251130062.png" alt="image-20220725113022786" style="zoom:67%;" />

# CS162 Lecture 8: Synchronization 3: Atomic Instructions (Con't), Monitors, Readers/Writers

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251109107.png" alt="image-20220725110948740" style="zoom:67%;" />

## Examples of Read-Modify-Write 

- **所有的这些都是一条原子指令所做的事情，每个伪代码都是原子操作。**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251313246.png" alt="image-20220725131356920" style="zoom:67%;" />

### Using of Compare&Swap for queues 

- 使用`Compare&Swap()`
- 无锁实现多线程下的同步：
  1. 将`root`的值（也就是旧头结点的地址）放入寄存器`r1`
  2. 将`r1`的值（也就是旧头结点的地址）放入新头结点（Object）的`next`域（让新头结点指向旧头结点）
  3. 调用`compare&swap()`
  4. 如果返回的是success，说明`root` 的值没有被其他线程修改，那么此时将root的值修改为新头结点（Object）的地址（让root指向新头结点）。
  5. 如果返回的是failure，说明`root`的值已经已经被其他线程修改，那么要放弃此次尝试所做的所有工作，回到1重新开始。
     - 因为返回的是failure的话，说明此前的头结点现在已经不再是头结点，而是有了新的头结点。
     - 从1重新开始才能保证Object指向的是正确的头结点。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251347147.png" alt="image-20220725134738865" style="zoom:67%;" />

- 也可以使用Compare&Swap实现一个锁：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211082155947.png" alt="image-20221108215526523" style="zoom:67%;" />

### Implementing Locks with test&set

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251418575.png" alt="image-20220725141835269" style="zoom:67%;" />

- 使用`test&set()`实现一个锁。

  - 不需要进入内核，所以开销小。

  - 

    ```c
    // (Free) Can access this memory location from user space!	int mylock = 0; 
    // Interface: acquire(&mylock);
    //			 release(&mylock);
    acquire(int *thelock) {
        while (test&set(thelock)); // Atomic operation!
    }
    release(int *thelock) {
        *thelock = 0;// Atomic operation!
    }
    test&set (&address) {/* most architectures */
        result = M[address];//return result from “address” and
        M[address] = 1;//set value at “address” to 1
        return result;
    }
    ```

- 利用`test&set()`的特性：

  - 返回值和`thelock`当前的值是一致的。
  - 无论当前锁是否被占用，都是将`thelock` 值置为1。
  - 也就是说，如果锁当前FREE，那么调用`acquire()`时候会将其置为BUSY，如果锁当前是BUSY，那么不会更改其状态。

- 基本原理：

  - 如果`thelock` 是FREE，那么`test&set()`返回的是0（同时将thelock置为1，即BUSY），`while()`循环退出，从`acquire()`退出，拿锁成功
  - 如果`thelock`是BUSY（1），那么`test&set()`将`thelock`置为1（不发生改变），同时返回的是1，`while()`循环继续。
  - 如果`release()`将`thelock`置为0，那么其他线程可以拿锁。
  
- 存在的缺点：

  - 忙等，浪费CPU时间
  - 对于多核：缓存一致性问题，每个核在等锁的时候都要不断重复写入1。
  - 优先级反转问题：假如低优先级的线程拿到了锁，高优先级线程就只能忙等
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251434536.png" alt="image-20220725143411309" style="zoom:67%;" />




### Multiprocessor Spin Locks: test&test&set

- 基于前一个实现锁的方法，针对多核进行了改进
- 放锁`release()` 之前是一样的
- 拿锁基本原理：
- 
  - 内层`while()`不断读`thelock` 值（只用读缓存就行了，也不用写，不会有缓存一致性问题）
  - 当`thelock`变为0，跳出内层循环，外层循环调用`test&set()`试图拿锁，成功了就跳出外层循环，失败了就再进入内层循环等待锁变为FREE）
- 问题：
  - 忙等
  - 但是大大减少了写入的次数，改善了多个核频繁修改缓存的问题。
  - **你没办法确定你到底要等多久，因为你不知道临界区到底有多长**
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251437972.png" alt="image-20220725143757743" style="zoom:67%;" />

### Better Locks using test&set

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251547424.png" alt="image-20220725154732186" style="zoom:67%;" />

- 基于前一个的改进：改进了忙等的时间，相比此前减少了很多。
- 全局变量`guard`，对guard做`while(test&set(guard))`，跳出循环后快速拿锁并释放`guard`。
  - 如果锁当前是BUSY，那么陷入休眠，并**同时**将guard置为0，从休眠中醒来的同时也拿到了锁（因为从休眠中醒来就从`acquire()`返回了，从`acquire()`返回的语义就是它拿到了锁）。
    - 为什么不在`sleep()` 前后将`guard`置为0？休眠后没办法将其置为0，休眠前将其置为0可能导致其他线程检查到guard为0，然后去拿锁（或者放锁）。
  - 在guard的忙等比对锁本身忙等要短得多（会忙等，但是时间不长，影响不大），并且前一个方法对于锁的忙等的时间长短是和拿到锁的线程的临界区的长度成正比的。
- 你还可能注意到了很有趣一点，当要唤醒另一个线程时，flag 并没有设置为 0。为什么呢？
  - 其实这不是错，而是必须的！线程被唤醒时，就像是从 Sleep()调用返回，从这个调用返回之后，立刻就是从`acquire()`返回，而从`acquire()`返回意味着线程是拿到了锁的，而拿到了锁，就意味着`flag`的值应该是1的。

- `guard`在这里的作用类似于之前利用开关中断来实现`acquire()`和`release()`。
  - 对guard做`test&set()`相当于关中断，将guard置为0相当于开中断。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207251552446.png" alt="image-20220725155211215" style="zoom: 50%;" />

### Linux futex: Fast Userspace Mutex

- 用来实现前一节提到的锁的`sleep`和`wakeup`：
  - 获取锁且发现锁被占用时的休眠
  - 释放锁且发现有其他线程在等待这个锁时的唤醒
- `futex`并不是c标准库的内容，而是用来实现`pthread` 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271142886.png" alt="image-20220727114243613" style="zoom:67%;" />

```c
#include <linux/futex.h> 
#include <sys/time.h> 
//uaddr:指向特定的锁变量
//futex_op:指定是拿锁休眠还是放锁唤醒
//val:如果uaddr指向的变量的值为val,进入休眠(或者唤醒val个线程，取决于是acquire还是release)
//timeout:没什么好说的
int futex(int *uaddr, int futex_op, int val, 
		  const struct timespec *timeout );
```

### Example: First try: T&S and futex

- `acquire()`的基本原理：
  - 情况1：`test&set()`返回的恰好是0，这个时候立即退出循环，立刻拿到锁
  - 情况2.1：`test&set()`返回的是1，说明锁被占用，进入循环，于是调用`futex()`告知其在`thelock`值为1时将其休眠。
    - 这种情况下，当拿到锁的线程调用`release()`释放锁时，`release()`里的`futex()`会负责唤醒的工作，唤醒之后继续下一个循环尝试拿锁。
  - 情况2.2：`test&set()`返回的是1，说明锁被占用，进入循环，于是调用`futex()`，但是此时`thelock`恰好被释放，于是`futex()`检测到`thelock`的值不为1，不将其休眠，而是立即进入下一次循环，继续尝试拿锁。
- `release()` 的原理很简单，**最后一个参数1指明最多唤醒一个线程**。
- 优点：
  - 使用`futex`避免了`acquire`的忙等
  - 拿锁的时候开销不大
- 缺点：
  - 放锁的时候每次都要进入内核来唤醒某个线程（即便没有线程在等待）
  - 我们需要的是当只有一个线程在拿锁和放锁时，不需要进入内核（尽可能的快），只有在多个线程要拿锁时才进入。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271202309.png" alt="image-20220727120247098" style="zoom:67%;" />

### Example: Try #2: T&S and futex

- 增加了一个`bool`类型`maybe_waiters`，用来表明是否存在其他线程等待这个锁。
- 增加这个变量后，就可以避免在没有其他线程等待这个锁时也进入内核尝试唤醒。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271302804.png" alt="image-20220727130237589" style="zoom:67%;" />

### Try #3: Better, using more atomics

- 原理：
  - 对于一个锁来说，有三种状态：UNLOCKED（没人持有锁）、LOCKED（一个线程持有锁）、CONTESTED（至少一个线程等待锁）
- acquire:
  - 先对锁的状态`compare&swap(thelock, UNLOCKED,LOCKED)`:如果锁是UNLOCKED，则将其置为LOCKED，拿到锁。
  - 否则进入`while()`循环尝试拿到锁，每次`swap()`返回LOCKED，就进入循环`futex`进行休眠。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271430687.png" alt="image-20220727143007446" style="zoom:67%;" />

## Semaphores are good but…Monitors are better!

- 监视器的定义：
  - 一个锁以及零个或多个同于管理并发访问共享数据的条件变量
  - 有些语言原生支持，有些使用实际的锁和条件变量
- 监视器是一种并发编程范式

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271450601.png" style="zoom:67%;" />

### Condition Variables

- 条件变量：处于临界区内等待某些条件的线程的队列
  - 中心思想：允许线程在临界区内发生睡眠，在发生睡眠的时候自动将锁释放（再醒来的时候会自动重新拿锁）。
  - 对比信号量：不能在临界区中中断（等待）
- 运算：
  - wait(&lock)：自动将锁释放，并且去休眠，然后在`wait()`返回之前自动将锁重新拿回来（这样就可以保证在临界区中执行时全程拿了锁的）
  - Signal()：唤醒一个等待的线程，如果有
  - Broadcast()：唤醒所有等待的线程（广播嘛）
- **当在做条件变量运算的时候，必须是持有锁的**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271452257.png" alt="image-20220727145205038" style="zoom:67%;" />

### Synchronized Buffer (with condition variable)

- 缓冲区的容量是无限的。
- `queue`是用来放东西的缓冲区
- `buf_CV`:条件变量，用于表示缓冲区是否空，最开始为空。
- 生产者：
  - 拿锁，放进去一个东西
  - `cond_signal(&buf_CV)`：唤醒正在等待的消费者
  - 放锁
- 消费者：
  - 拿锁
  - 在缓冲区是空（缓冲区没有东西）的时候，进入`cond_wait()`
    - 传入`buf_lock`是为了休眠的时候顺便把锁释放掉，以及返回之前重新拿回锁
  - 从`cond_wait()`返回后，继续检测缓冲区是否为空(为什么？），如果不为空，那么就拿东西，放锁。
    - 为什么：考虑以下情况：
      - 线程A进入`cond_wait()`，放锁，休眠。
      - 生产者放入一个东西，然后`signal()`线程A。
      - 线程B拿到锁，然后`while()`循环检测到缓冲区不为空，拿走放进去的那个东西，缓冲区重新变空，线程B放锁。
      - 线程A重新拿到锁（因为被唤醒了），假如不检测，那就从空缓冲区拿东西了。
      - 实际情况是线程A重新拿回锁，又一次检测到缓冲区为空，然后继续休眠。
  - `while()`那个位置究竟是`if()`还是`while()`需要看语义，有两种不同的监视器（Mesa vs. Hoare monitors）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271557529.png" alt="image-20220727155717279" style="zoom:67%;" />

### Mesa vs. Hoare monitors

- `while()`是mesa Style，`if()`是Hoare-style。
- 大多数操作系统实现的都是mesa style。因为Hoare style的要求太严格（要求唤醒之后下一个被调度到的一定是被唤醒的那个）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271615745.png" alt="image-20220727161541483" style="zoom:67%;" />

### Hoare monitors

- 要求：`signal()`之后立即将CPU和锁给那个等待着的线程，然后那个线程立即运行
  - waiter退出临界区或者再次`wait()`的时候将锁和CPU交给`signaler`（当然，在这里就是做完正事直接放锁退出临界区，因为是从`if()`跳出）
- 低效，很多强制性的上下文切换
  - 一定要从Signaler到waiter，或者反过来
  - 本来Signaler运行得好好的，一堆东西缓存起来了，结果一调用`signal()`Signaler就立即失去了它的CPU，缓存的东西也丢了。
- 这里能够采用`if()`而不是`while()`就是因为在`signal()`和从`cond_wait()`返回之间，条件没有发生变化（因为waiter是立即运行的）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271619595.png" alt="image-20220727161957360" style="zoom:67%;" />

### Mesa monitors

- Signaler在调用`cond_signal()`之后仍然保有CPU和锁，调用`signal()`只是将`waiter()`放入就绪队列。
  - 过一段时间之后，调度器调度几次（可能一次可能多次），然后waiter醒来，然后继续检查条件。
- 所以`waiter()`需要在从`cond_wait()`返回之后再重新检查条件。
  - 因为在它检查条件的时候，可能由于之前其他线程的影响，条件重新变为false。
- 大多数OS这样。更好实现

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271636446.png" alt="image-20220727163619207" style="zoom:67%;" />

### Circular Buffer – 3rd cut (Monitors, pthread-like)

- 看代码就行，很简单，CV：Condition Variable
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271641807.png" alt="image-20220727164146545" style="zoom:67%;" />

### Again: Why the while Loop?

- 一是MESA的语义
- 二是，waiter有可能并不是在`signal()`之后就立即重新拿到锁的，也许有第三个线程被先调度，然后“悄悄溜进”缓冲区拿走唯一的一个东西。
  - 这个时候条件就被第三个线程改变了，所以需要一个循环来重新检查醒来时的条件变量。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271642682.png" alt="image-20220727164240442" style="zoom:67%;" />

## Readers/Writers Problem

- 有关读写锁。上海交大讲的很清楚了。

### Basic Readers/Writers Solution

- 读者可以在没有写者的时候访问数据库
- 写者可以在既没有读者也没有写者的时候访问数据库
- 一次只能有一个线程操纵状态变量（比如读者和写者的计数器）
- 读者：
  - 等待直到没有写者
  - 访问数据库
  - 退出，检查是否有写者需要唤醒
- 写者：
  - 等待直到没有活跃的读者和写者
  - 访问数据库
  - 退出，检查是否有读者或者写者要唤醒
- 状态变量：看图

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271723527.png" style="zoom:67%;" />

### Code for a Reader

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271726387.png" alt="image-20220727172616165" style="zoom:67%;" />

### Code for a Writer

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207271727270.png" alt="image-20220727172706040" style="zoom:67%;" />

# CS162 Lecture 9: Synchronization 4: Monitors and Readers/Writers (Con't), Process Structure

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281312042.png" alt="image-20220728131201683" style="zoom:50%;" />

## Simulation of Readers/Writers Solution

- 模拟的过程很简单，问题也之前解答过了

### Questions

- 读者是否会饥饿致死？
  - 会的，因为这是一个偏向写者的锁，如果在读者进入休眠之后源源不断的有写者到来，那么写者会一直先越过读者，读者没有机会读。
- 如果在读者退出时不对ActiveReader == 0 && WaitingWriter > 0的条件做检查，就直接`cond_signal()`会怎么样？
  - 最终还是能够正常工作，只是会浪费一些不必要的CPU时间。
  - 考虑情况1：还有活跃的读者，并且有等待的写者：
    - 一个写者被唤醒，回到`while()`开头重新检查条件，发现此时不能写，于是进入while循环内继续休眠
    - 读者则继续读。
  - 情况2：无活跃读者，无等待写者，那么什么都不发生。
  - 情况3：有活跃读者，无等待写者：无事发生。
  - 情况4：无活跃读者，有等待写者：正是我们代码中检查的条件。
  - 其他情况都很简单，情况1是最主要的。
- 如果将signal()替换为`broadcast()`：
  - 那么所有的写者都会被唤醒，但是只有一个写者能够成功，原理与前一个问题一样。
- 最后一个问题没什么意义。不是一个好的选择。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281433654.png" alt="image-20220728143318378" style="zoom:67%;" />

## Can we construct Monitors from Semaphores?

- 使用信号量来建立监视器：

  - 锁的部分使用互斥锁实现即可

- Can we implement condition variables this way?

  - ```c
    Wait(Semaphore *thesema){ 
        semaP(thesema);
    }
    //wait的时候没有把锁释放(监视器在休眠之前要放锁的),会导致死锁
    Signal(Semaphore *thesema){
            semaV(thesema);
    }
    ```

- Does this work better?

  - ```c
    Wait(Lock *thelock, Semaphore *thesema) {
        release(thelock);
        semaP(thesema);
        acquire(thelock);
    }
    Signal(Semaphore *thesema) { 
        semaV(thesema);
    }
    ```

  - wait()还是不满足要求

  - 考虑以下情况：

  - 连续做了三次Signal，将信号量由0变为3

  - 然后做一次wait()，**由于信号量此时是大于零的，P可以直接将信号量减一，而不进行休眠**，这不符合wait()的语义。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281558255.png" alt="image-20220728155816994" style="zoom: 60%;" />

### Construction of Monitors from Semaphores (con’t)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281619426.png" alt="image-20220728161932155" style="zoom:67%;" />

## Mesa Monitor Conclusion

- 读写锁就是一个很好的应用实例

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281623800.png" alt="image-20220728162333541" style="zoom:67%;" />

## Language Support for Synchronization

- 讲了一堆语言对同步的支持

## Recall: User/Kernel Threading Models

### Kernel Structure So Far (1/3)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281643510.png" alt="image-20220728164358276" style="zoom:67%;" />

### Kernel Structure So Far (2/3)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281644355.png" alt="image-20220728164417106" style="zoom:67%;" />

### Kernel Structure So Far (3/3)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207281644448.png" alt="image-20220728164449125" style="zoom:67%;" />

# CS162 Lecture 10: Scheduling 1: Concepts and Classic Policies

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291411683.png" alt="image-20220729141120379" style="zoom:67%;" />

## Layers of I/O…

- 高层I/O
- 底层I/O
- 系统调用
- 文件系统
- I/O驱动
- 硬件

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291550462.png" alt="image-20220729155011150" style="zoom:67%;" />

## Device Drivers

- 设备驱动：内核中特定于设备的、直接与设备硬件交互的代码。
  - 支持一个标准化的内部接口
  - 底层的内核I/O能够轻易地与不同的设备驱动交互
  - 特定的设备有关的配置
- 设备驱动一般分为两部分：
  - 顶层部分：能从系统调用去调用的部分
    - 实现了一系列的标准、跨设备的调用，比如open、close等。
    - 顶层部分开始对设备的I/O，可能会将线程休眠直到I/O完成
  - 底层部分：作为中断程序运行
    - 获取输入或者传输下一块的输出，取决于是什么类型的中断
    - 可能会唤醒休眠的线程，如果I/O完成。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291551667.png" alt="image-20220729155125436" style="zoom:67%;" />

## Life Cycle of An I/O Request

- 一个I/O请求的生命周期：从左往右、从上往下



1. 用户程序发出I/O请求，通过系统调用进入内核线程
2. 内核检查是否已经满足要求（为什么？），如果满足要求直接向上返回，把要求的数据给用户程序
   - 因为存在内核的缓存，内核可能会一次性从硬盘拉出一大块数据，然后缓存起来
3. 如果不满足I/O请求，就将请求发送给设备驱动，同时可能会将线程或者进程休眠
4. 来到设备驱动的TopHalf，一直往底层下去，处理请求
5. 自底向上返回

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291600419.png" alt="image-20220729160048148"  />

## Goal for Today

- 调度器处于一个无限循环中：
  1. 如果有就绪线程，选择下一个运行的线程，运行它
  2. 如果没有，运行一些idle线程
  3. 回到1
  4. <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291610647.png" alt="image-20220729161020404" style="zoom: 60%;" />
- 问题：
  - 循环的频率？
  - 怎么选择下一个？（调度策略）
  - 什么时候打断一个线程，选择另外一个？
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291614143.png" alt="image-20220729161447926" style="zoom:60%;" />

## Scheduling Assumptions

- CPU调度的一些假设：
  - 每个用户一个程序
  - 每个程序一个线程
  - 程序之间相互独立
- 显然这些假设是不现实的，但是可以简化问题
  - 比如，公平是相对用户公平还是相对每个进程公平？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291638285.png" alt="image-20220729163848030" style="zoom:67%;" />

### Assumption: CPU Bursts

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291648920.png" alt="image-20220729164856550" style="zoom:80%;" />

## Scheduling Policy Goals/Criteria

- 最小化响应时间：
  - 比如敲击键盘到字符出现在编辑器上
  - 比如实时系统
- 最大化吞吐量：最大化单位时间内能做的操作数量
  - 吞吐量与响应时间有关，但不是完全相同。最小化响应时间会比单纯的最大化吞吐量带来更多的上下文切换
  - 最大化吞吐量的两个方面：
    - 最小化开销，比如上下文切换
    - 对资源的有效利用
- 公平性：
  - 公平性并不意味着最小化响应时间，因为优化响应时间的方法往往是使得系统没那么公平（要优先响应）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291649026.png" alt="image-20220729164938776" style="zoom:67%;" />

## First-Come, First-Served (FCFS) Scheduling



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291701097.png" alt="image-20220729170119782" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202207291706070.png" alt="image-20220729170627807" style="zoom:67%;" />

- 先来先服务，FIFO
  - 早期FIFO的意思是，一个程序一直运行知道它完全做完所有工作退出（包括I/O）
  - 现在FIFO的意思是，程序一直运行直到其被阻塞（比如I/O），此时才被调度让出CPU。
- 优点：简单
- 缺点：短任务很容易被阻断在长任务的后面，导致整体平均指标偏长，**进程到来的次序对平均等待时间的影响是很大的**
  - 比如例子的三个进程P123，如果按照123的次序到来，整个系统的平均指标比按照231的次序到来要差了不少

## Round Robin (RR) Scheduling

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011203198.png" alt="image-20220801120324888" style="zoom:67%;" />

- 时间片轮转/轮询调度
- FIFO的问题：潜在的不利于用时短的工作，依赖于任务到来的顺序
- 时间片轮询：
  - **抢占式**的调度策略
  - 每个进程得到一个小的CPU时间片，通常是10-100毫秒
  - 在时间片结束之后，进程被其他进程取代并且加入到就绪队列的尾部
  - 队列中有n个进程，时间片的长度是q的情况下：
    - 每个进程得到CPU时间的$1\over n$
    - 以最大长度为q时间片为块（可能更短，比如用时只需要1毫秒的任务）
    - 任何进程都等待不超过$(n-1)q$的时间



- 性能分析：
  - 在时间片的长度q很大的时候（取比耗时最长的任务还长的时间），性能回归到FIFO（先来先服务）
  - q小的时候：进程之间穿插很多，特别小：超线程？
  - q必须要相对上下文切换的开销来说较大，否则开销太大，大部分时间在做频繁的上下文切换
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011209303.png" alt="image-20220801120952067" style="zoom:67%;" />

### Example of RR with Time Quantum = 20

- **P2并没有占用完整的时间片，而是8个单位的时间**
- RR对于短的任务来说是更公平的，但是对于长任务来说，上下文切换的时间累积更多（因为长任务需要更多的时间片）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011223418.png" alt="image-20220801122322170" style="zoom:67%;" />

### Decrease Response Time

- 相对于先来先服务策略来说，响应时间是减少了的
- 上面取Q等于10，就直接回归了先来先服务策略
- 下面取Q等于1，平均的响应时间就减少了
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011226696.png" alt="image-20220801122636464" style="zoom:67%;" />

### Same Response Time

- 当然并不是时间片Q取得越小越好，上下文切换的开销决定了不能这样
- 也不是时间片Q的长度越小，响应时间就越短，下面的两种情况之下Q的取值差了十倍，响应时间却是一样的。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011241016.png" alt="image-20220801124157775" style="zoom:67%;" />

### Increase Response Time

- 甚至Ｑ如果取得太小，响应时间反倒会变的更长
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011243110.png" alt="image-20220801124310872" style="zoom:67%;" />

### How to Implement RR in the Kernel?

- 一个FIFO队列，和先来先服务策略是一样的
- 但是在时间片到期之后就抢占工作，然后把进程丢到队列的尾部

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011243216.png" alt="image-20220801124344959" style="zoom:67%;" />

### Round-Robin Discussion

- 如何选择时间切片？
  - 太长：响应时间长
  - 取无限长：回归先来先服务
  - 太短：吞吐量不足，上下文切换多
- 实践的时间片选择：
  - 典型的时间片是10-100毫秒
  - 上下文切换的开销是0.1-1毫秒
  - 保证由上下文切换产生的开销大约占百分之一。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011245272.png" alt="image-20220801124505024" style="zoom:67%;" />

### Comparisons between FCFS and Round Robin

- 在FIFO和RR之间比较，是永远都是RR更优吗？
- 假定10个任务，每个任务需要100个单位CPU时间，RR的时间片长度为1个单位。**并且假定上下文切换没有开销**
- 表格中列出了十个工作完成的时刻表：
  - 采用RR时，前99轮，没有一个任务完成了，都差1个单位的CPU时间，此时过去的总时间是990个单位。因此任务1在991时刻完成。
- 由表可以得出的结论：
  - 两种调度策略之下，RR和FCFS在同一个时刻完成全部10个任务。
  - **平均响应时间，RR远差于FCFS**
    - 也就是说在所有的工作需要的时长相同时，RR表现不好。
  - **假如还要考虑上下文切换的开销的话，RR的上下文切换次数要远大于FCFS，因此最后综合表现会差很多**
- 同时，采用RR策略时，Cache必须在所有的工作之间共享（轮流使用一小段时间），而FCFS则可以完全投入一个工作（直到工作完成）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011403644.png" alt="image-20220801140335373" style="zoom:67%;" />

### Earlier Example with Different Time Quantum

- RR存在的意义是什么？
  - 给一个不那么差的调度综合表现，虽然达不到最好，但是也不至于是最差的，中规中矩。
  - 从表格可以看出，RR的表现是介于最好的FCFS和最差的FCFS之间的。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011410895.png" alt="image-20220801141043629" style="zoom:67%;" />

## Handling Differences in Importance: Strict Priority Scheduling

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011415303.png" alt="image-20220801141538038" style="zoom:67%;" />

- 基本思路：
  - 永远先启动最高优先级的可运行任务，直到其完成
  - 同优先级队列内，采用RR，也就是说，同优先级的队列中所有任务都完成了，才轮到次优先级的队列。
- 问题：
  - 饥饿：低优先级的任务可能会因为高优先级的任务太多，而得不到运行的机会
  - 死锁：优先级反转
    - 低等级的任务持有高等级任务需要的锁，导致高等级任务不能运行
    - 经常涉及第三个优先级介于二者之间的任务比高优先级的任务更先运行的情况。
- 如何解决：
  - 动态优先级，优先级继承什么的。

### Scheduling Fairness

- 基于优先级的调度，肯定是不公平的调度策略。
- 公平性是牺牲平均响应时间得到的，tradeoff永不过时。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011425618.png" alt="image-20220801142538367" style="zoom:67%;" />



- 怎样实现公平性？
  - 给每个队列一定的CPU时间
  - 调整一直没有得到服务的工作的优先级。
    - 但是并不好确定怎么调整是合适的，并且没有一个永远适用的答案。
    - 并且，当系统过载时，所有的任务都会提升自己的优先级，情况并没有改善。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011427434.png" alt="image-20220801142729179" style="zoom:67%;" />

## What if we Knew the Future?

- 如果可以预知未来的话，就有其他的几种调度策略：
  - 最短工作优先
  - 最短剩余时间优先
- 最短工作优先：
  - 优先运行要做的工作最少的任务
- 最短剩余时间优先：
  - 最短工作优先的抢占版本
  - 当前新到来的任务的完成时间比当前正在运行的任务的剩余时间短时，新到来的任务抢占CPU。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011431649.png" alt="image-20220801143131384" style="zoom:67%;" />

### Discussion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011436227.png" alt="image-20220801143605971" style="zoom:67%;" />

- 最短工作优先和最短剩余时间优先是最小化平均响应时间的最优解
- 比较二者：
  - 如果所有的工作的长度一样：SRTF和FCFS是一样的（FCFS在所有工作长度一样时是最优的）
  - 工作的时间变化很大：SRTF，短工作不会被阻塞

### SRTF Further discussion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011441360.png" alt="image-20220801144156094" style="zoom:67%;" />

- 饥饿：
  - 如果有太多的短工作，SRTF可能会导致长任务处于饥饿状态。
- 需要预测未来：
  - 怎么做？
  - 有些系统向用户询问，但是用户不一定是诚实的。
- 底线就是不能预测一个任务用时要多久，但是可以用SRTF来作为参照物衡量其他的调度策略，因为SRTF是最优的策略。

### Predicting the Length of the Next CPU Burst

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011455494.png" alt="image-20220801145523206" style="zoom:67%;" />

## Lottery Scheduling

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011456434.png" alt="image-20220801145610173" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011458761.png" alt="image-20220801145817493" style="zoom:67%;" />

## How to Evaluate a Scheduling algorithm?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208011504198.png" alt="image-20220801150455765" style="zoom: 67%;" />

# CS162 Lecture 11: Scheduling 2: Case Studies, Real Time, and Forward Progress

## How to Handle Simultaneous Mix of Diff Types of Apps?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021411639.png" alt="image-20220802141121298" style="zoom:67%;" />

- 当机器上同时存在高交互性的以及高吞吐的应用时：
  - 如何最好地调度？
  - 如何区分是前者还是后者？是信任App声称自己是高交互性的？
  - 是否在所有的平台（服务器、PC、Pad、手机）上，调度的策略都完全一样？
- 许多调度器的假设：
  - 频繁休眠以及有许多短突发的App是交互性的，应该有更高的优先级
  - 有大量计算的App应该有更低的优先级，因为它们注意不到那些间歇性出现的来自交互性App的短突发。
  - 但是还是很难刻画App的特征，因为总有一些App是不满足上述两点，但是又有交互性需求的。

## Multi-Level Feedback Scheduling

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021425854.png" alt="image-20220802142516574" style="zoom:67%;" />

- 多级反馈调度：
  - 多个队列，每个队列有不一样的优先级。
    - 高优先级的队列通常看做是前台任务
  - 每个队列有属于自己的调度策略。
    - 比如前台任务的队列使用RR，同时时间片长度取8
    - 后台任务队列使用FCFS，而介于二者之间的使用RR，但是时间片取16。
    - 有时多个RR队列的情况下，RR队列的时间片长度是指数递增的。优先级越高的，时间片长度越短。
- 按照如下方式调整每个任务的优先级：
  - 从最高优先级开始。
  - 如果时间片到期（而任务还没干完），下调一个优先级
  - 如果时间片有余（还没到期就干完，或者遇到I/O），上调一个优先级。

### Scheduling Details

- 结果近似SRTF，毕竟是利用了过去的行为预测未来。
- 队列与队列之间也需要调度：
  - 固定优先级的调度：先服务最高优先级的队列，然后下一个优先级，然后再下一个。**可能导致饥饿问题**。
  - 时间分片：给每个优先级队列一个固定比例的CPU时间。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021437030.png" alt="image-20220802143737761" style="zoom:67%;" />

- 同时这种调度策略很容易受到用户的反制措施的影响。
  - 用户可以通过一些行为来反制，使得OS设计者的调度意图失败。
  - 比如在任务中插入一系列的无意义I/O来提升这个任务的优先级，从而得到更多的CPU时间
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021442974.png" alt="image-20220802144210721" style="zoom:67%;" />

## Case Study: Linux O(1) Scheduler

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021453659.png" alt="image-20220802145314372" style="zoom:67%;" />

- 140个优先等级。
  - 等级0的优先级最高，等级139的优先级最低。
  - 前一百个高优先级用于内核任务或者实时任务，后40个低优先级用于用户任务。
  - 所有的算法复杂度都是$O(1)$的，所以叫O(1)调度器。
    - 两个细节后面讲。
- 两个独立的优先队列，一个叫`active`，一个叫`expired`
  - 处于`active`队列中的任务使用完自己的时间片之后就会被放入`expired`队列，在`active`队列空后，交换两个队列。
- 时间分片随优先级线性增长。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021522481.png" alt="image-20220802152246218" style="zoom:67%;" />

### O(1) Scheduler Continued

真几把复杂

- 这玩意太复杂，基本上没人能理解它为什么可以工作，现在已经没人用了，在Linux中已经被抛弃了。
- 用户任务的优先级可以动态调整正负5。
- IC：活跃分？
  - 当一个任务休眠长时间的时候加分
  - 当一个任务运行长时间的时候减分
  - IC用于提供一些阻滞性，避免由于行为的暂时改变而改变交互性
- 实时任务：
  - 永远抢占非实时任务
  - 没有动态的优先级
  - FIFO或者RR

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021524432.png" alt="image-20220802152450163" style="zoom: 67%;" />

## So, Does the OS Schedule Processes or Threads?

- OS实际上调度的是线程，而不是进程。
- 切换线程和切换进程的开销是差别很大的：
  - 切换线程只需要保存和加载寄存器，但是切换进程则还需要更改地址空间，更改地址空间开销很大，并且还会扰乱缓存。
- 对于超线程，又不一样，切换线程的时候需要切换地址空间。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021551296.png" alt="image-20220802155146027" style="zoom:67%;" />

## Multi-Core Scheduling

- 算法上与单核调度区别不大，实现方面，每个核有一个调度的数据结构更方便
- 关联调度：一旦在CPU上调度线程，操作系统就会尝试在同一个CPU上重新调度它
  - Cache重用

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021625650.png" alt="image-20220802162549398" style="zoom:67%;" />

### Recall: *Spinlocks for multiprocessing*

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021629098.png" alt="image-20220802162909818" style="zoom:67%;" />

### Gang Scheduling and Parallel Applications

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021629019.png" alt="image-20220802162941759" style="zoom:67%;" />

## Real-Time Scheduling

- 实时性调度的目标：
  - 在传统的调度中，目标是系统的吞吐量、响应时间等等
  - 而实时性调度是能够保证在最坏的情况下也能及时响应（踩刹车，然后车子不管什么情况都能刹住）。
- 硬实时：针对时间关键型、面向安全性的系统。调度不及时就会死人。
  - 比如刹车。
  - 如果可以，必须满足所有的DDL，提前确定。
  - 调度器：EDF、LLF、RMS、DM
- 软实时：多媒体。比如确保视频的每一帧都能按时到达，但是出错了并不会出大事。
  - 试图满足很可能满足的DDL。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208021634259.png" alt="image-20220802163408981" style="zoom:67%;" />

# CS162 Lecture 12: Scheduling 3: Deadlock

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208051521304.png" alt="image-20220805152122961" style="zoom:67%;" />

# CS162 Lecture 13: Memory 1: Address Translation and Virtual Memory

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061343292.png" alt="image-20220806134339973" style="zoom:67%;" />

## Recall: Process Address Space: typical structure

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061450425.png" alt="image-20220806145042170" style="zoom:67%;" />

## Recall: Single and Multithreaded Processes

- **一个进程内，线程之间共享代码**

![image-20220806145114260](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061451551.png)

## Important Aspects of Memory Multiplexing

- 保护：
  - 比如保护一个进程不能访问和修改另外一个进程的私有内存、任意一个进程都不能去修改操作系统的内存
  - 比如将某将进程的某一块设置为只读，以起到防止修改的作用（比如将进程的代码段设置为只读，代码在加载进内存之后本来就应该是只读的，这样线程之间就可以安全地共享代码）
  - 比如将某块内存设置为用户程序不可见
- 翻译：
  - 从虚拟地址到物理地址的翻译。
  - 当有虚拟地址的时候，处理器可以使用虚拟地址，而物理内存使用物理地址
  - 好处：避免进程之间内存空间的重叠、给程序提供一个统一的内存视角
- 受控制的内存重叠：
  - 比如线程之间不应该在物理内存上有重叠，预期外的重叠会导致程序出错
  - 比如想实现进程之间通信时，就可能需要两个进程共享一块内存。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061549126.png" alt="image-20220806154925858" style="zoom: 67%;" />

## Binding of Instructions and Data to Memory

- 当没有虚拟地址的机制时，如果想要同时运行同一个程序的多个实例，那么在链接和加载时就需要对代码中的地址进行重定位，并且每运行一次，就要重定位一次，因为每次运行，程序所处于的物理内存可能是不一样的位置。
- 运行第一个App X实例：将其放置在`0x300`开始
  - 运行第二个App X实例，将其放置在`0x1300`开始，载入内存前修改了代码中的内存地址信息，也就是重定位。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061602505.png" alt="image-20220806160216235" style="zoom:67%;" />
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061605886.png" alt="image-20220806160527591" style="zoom:67%;" />

## From Program to Process

- 地址的重定位可以发生在这个流程的任意一处，取决于操作系统和硬件。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061611005.png" alt="image-20220806161102709" style="zoom:67%;" />

## Recall: Uniprogramming

- 一台计算机，一次只能运行一个App，这一个App占用一整台机器，也就事实上占用了整个内存
- App永远在物理内存的同一个地方运行，不会发生重叠因为一次只有一个App
- App可以访问完整的物理内存，可以访问任意一个物理地址
- 没有地址翻译、没有保护。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061612034.png" alt="image-20220806161208748" style="zoom:67%;" />

## Primitive Multiprogramming

- 没有地址翻译和保护的多程序，需要在将程序载入内存之前进行重定位以保证不会发生重叠。
- 缺少保护所以某个程序可能会直接修改系统内存，造成崩溃。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061625350.png" alt="image-20220806162545054" style="zoom:67%;" />

## Recall: Base and Bound (No Translation)

- Base和Bound是这个程序的内存地址上下限，任意一个地址都会先检查是否处于区间内，然后再做操作。
- 仍然需要重定位。有了保护作用，能保护OS和隔离程序。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061649792.png" alt="image-20220806164930464" style="zoom:67%;" />

## Recall: General Address translation

- MMU负责将虚拟地址翻译为物理地址。
- CPU视角：使用虚拟地址和虚拟内存，程序所见到的视角。
- 内存视角：使用物理地址和物理内存。
- MMU负责在两种视角之间进行转换。
- 有了地址翻译之后，实现保护就更加容易。
- 有了地址翻译之后，每个程序都能被链接和加载在用户地址空间的同一个区域中。
  - 每个程序都能可以认为自己拥有地址0和地址114514
  - 无论程序在物理上运行在哪里，都只需要链接和加载一次即可，因为无论物理上加载在哪里，虚拟地址都是一样的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061651990.png" alt="image-20220806165123710" style="zoom:67%;" />

## Recall: Base and Bound (with Translation)

- 硬件重定位
- Base是基地址，但是Bound是偏移量的上限值，而不是地址的上限值。
- 从处理器来的地址是虚拟地址，作为偏移量。
- 偏移量加上基地址，得到物理地址。
- 在进行加法之前，会先检查处理器给出的虚拟地址是否超出了Bound。
- 能够保护OS，也能隔离不同的进程。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208061701310.png" alt="image-20220806170138032" style="zoom:67%;" />

### Issues with Simple B&B Method

- 最主要的问题是内存碎片化的问题（以及内部碎片的问题，如OSTEP所讲）。
- 如下图：
  - 最开始是进程6 5 2和OS，然后进程2结束留下内存空洞
  - 然后进程9出现，占用空洞的一部分
  - 然后进程5结束，同时进程10出现，将剩余的内存空洞划分为散落的两块。
  - 然后进程11想要加入其中，但是由于两块空闲内存各自独立的大小都不足，所以进程11没办法加入（但是两块内存空洞加起来却足够了）
  - 因此可以看出，随着时间推移，空闲内存会逐渐地碎片化。
- 缺少对离散的地址空间的支持。
  - 意思是说，能够将一个程序的内存的不同部分划分为离散的不同的块，可以分别映射到物理内存。而不是整个地址空间映射到一块连续的物理内存上，而是比如代码段在一块连续的内存上，栈在另外一块，堆也在另外一块。
- 很难做到进程之间的内存共享：
  - BaseANDBound本身的机制就决定了进程无法访问界限以外的内存。
  - 想要做到进程间通信就可能只能通过系统调用的形式，比如使用管道。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208062131489.png" alt="image-20220806213141156" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208191429910.png" alt="image-20220819142909542" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208191431354.png" alt="image-20220819143154034" style="zoom:67%;" />

### More Flexible Segmentation

- 解决问题的方法就是，允许一个程序的地址空间能离散分布在多块连续的内存上，而不是整个地址空间都在一块连续的内存上。
  - 比如地址空间可以分成堆、栈、代码段等等，堆在一块连续的物理内存中，栈在另外一块连续的物理内存中，代码段又在另外一块。
  - 通过将两个进程中各取一块逻辑段映射到同一块物理内存，就可以实现共享内存。
    - 比如将两个进程的绿色段4号都映射到物理内存的绿色4号块，这样两个进程就共享内存，可以实现通信。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071253772.png" alt="image-20220807125335416" style="zoom:67%;" />

- **分段则存在外部碎片问题**
  - 解决碎片化问题，一是使用好的分配算法，比如伙伴算法，二是将碎片化的物理内存整合起来，但是开销很大。
  - 分段还是不足以支持更一般化的稀疏地址空间。例如，如果有一个很大但是稀疏的堆，都在一个逻辑段中，整个堆仍然必须完整地加载到内存中。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208191513028.png" alt="image-20220819151348671" style="zoom:67%;" />

### Implementation of Multi-Segment Model

- 讲的是上一节的模型的实现
- 借助最开始的`Base&Bound`，只不过此前只有一块内存和一对`Base &Bound`，这里有八对，且每一对都有一个标志位用于标志是否合法。
  - 有多少块物理块，就有多少对`Base&Bound`表项
- Limit：Bound，可以说是偏移量的上限值。V：Valid，表示这一块合法（进程不一定八块都有）。N：Not Valid，不合法。
- 将虚拟地址分段，Segmentation段和Offset段，分别指定这个地址在哪一块，以及在那一块中的偏移量。
  - 八个段的情况下，Segmentation段为3比特。
  - 将Offset和Limit比较以确定偏移量是否合法，检查合法标志位确定段是否合法。
  - 虚拟地址可以来源于指令的某一部分，比如x86架构。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071316642.png" alt="image-20220807131604358" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208191437705.png" alt="image-20220819143718415" style="zoom:67%;" />

### Intel x86 Special Registers

- 和上一节的模型很相像，只不过这里分为了六段（Code Seg，Stack Seg，etc）
- `Base&Bound`寄存器也不是直接存储Base和Limit，而是存储了一个表的索引，通过索引去查表的某一项，那个表项就是它的Base&Bound
- RPL：Request Privilege Level，表示特权级是内核态还是用户态

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071337405.png" alt="image-20220807133742085" style="zoom:67%;" />

### Example: Four Segments (16 bit addresses)

- 这个是地址空间分段模型的例子

- 左边是虚拟地址空间，右边是物理地址空间。
- 虚拟地址的高两位是段ID(Seg ID)，查表看Base和Limit
  - 0x000SegID为0
  - oxC00的SegID为3因为虚拟地址的二进制形式的最高2位为`11`。
- 黄色的块为多个进程之间共享的物理内存，代码段(Seg 1)也可能被共享。
- **绿色的表也是属于上下文切换时要切换的一部分，每当CPU从一个进程切换到另外一个进程，绿色的表也要先保存旧的然后加载新的**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071351745.png" alt="image-20220807135124442" style="zoom:67%;" />

### Example of Segment Translation (16bit address)

- 这个是地址翻译的例子。

- 绿色的表所代表的寄存器只有OS可以修改，只有处于内核态时可以修改。
- **只有在需要访问物理内存的时候才会去做从虚拟地址到物理地址的翻译**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071402971.png" alt="image-20220807140233663" style="zoom:67%;" />

### Observations about Segmentation

- 只有在取指令、加载和存储的时候会做翻译，换言之访问物理内存的时候
- 虚拟地址空间使得虚拟内存之间可以存在空洞（如下图）
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071432215.png" alt="image-20220807143200941" style="zoom: 67%;" />
- 什么时候访问合法范围之外的地址是可以的？
  - 当要给堆和栈分配更多内存空间的时候
- 需要在段表中有保护模式。比如段表中的代码段需要设置为只读的，数据和栈段需要设置为可以读写的
- **段表是保存在CPU中的，不是内存中的（因为小），当上下文切换的时候也需要保存旧的和加载新的**
  - 将旧的段表存入内存，新的段表从内存载入CPU
  - Swap。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071427312.png" alt="image-20220807142731014" style="zoom:67%;" />

### What if not all segments fit in memory?

- 如果内存空间不够存入所有进程的所有段怎么办？Swap
- 将内存中的某些进程存入磁盘中，给下一个进程腾出内存空间。
- Swap是极端形式的上下文切换，开销比普通的上下文切换大得多。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071451614.png" alt="image-20220807145111283" style="zoom:67%;" />

### Problems with Segmentation

- 碎片问题，内部碎片和外部碎片。
  - 内部碎片：已分配的内存大于需要的内存，已分配的内存中存在没使用的部分
  - 外部碎片：夹杂在已分配的内存块之间的未分配的内存碎片。
- 为了把一切都装入内存可能需要移动进程多次（从而合并多个外部碎片）
  - 单个外部碎片放不下新来的进程，但是多个碎片合起来就可以，所以要移动其他的进程在内存的位置来合并碎片。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071455241.png" alt="image-20220807145459939" style="zoom:67%;" />

## Paging: Physical Memory in Fixed Size Chunks

- 将物理内存以固定大小的块（page，页）来分配，每块物理内存的大小是一样的。
  - 能够使用一个比特向量来记录物理内存的分配情况，0表示某一页未被分配，1表示某一页已经被分配
  - 页的大小不应该和此前的段大小一样，否则会导致许多的内部碎片。
    - 后果：需要多个页

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071525476.png" alt="image-20220807152530172" style="zoom:67%;" />

### How to Implement Simple Paging?

- 最简单的分页，接近于实践的做法。
- 一个进程一个页表，页表存储在内存中，而不是存在CPU中，页表包含物理页和每个虚拟页的权限（合法比特位、读、写等等）
- 虚拟地址分为两部分
  - 虚拟页号
  - 偏移量。偏移量是在物理页中的偏移量。
- 将虚拟地址的偏移量部分和物理页表的页号拼接在一起就得到了物理地址。
- 页表地址寄存器存储页表的地址，指向页表。
- 需要检查虚拟页号是否超过了页表的尺寸，比如页表只有4，虚拟页号出现6是不合法的。
- 得到物理地址后还需要检查权限，是否合法，是否能够读写。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071535631.png" alt="image-20220807153512328" style="zoom:67%;" />

### Simple Page Table Example

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071547544.png" alt="image-20220807154705253" style="zoom:67%;" />

### What about Sharing?

- 看图就好
- 应该是一个生产者消费者模型，因为两个进程的读写权限不一样
- 有一点很奇怪就是，同一块物理页没有被映射到两个虚拟页表的同一号表项上去，一个是4一个是2。
  - 如果将物理页都映射到同一个虚拟页，那么两个进程可以使用同一个虚拟地址来访问同一个物理内存位置
  - 反之，两个进程要访问同一个物理内存，在两个进程中的虚拟地址是不一样的。
  - 可以使用系统调用来满足需求


<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081422100.png" alt="image-20220808142159712" style="zoom:67%;" />

### Where is page sharing used ?

- 第一点没太看懂

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071636523.png" alt="image-20220807163629216" style="zoom:67%;" />

## Memory Layout for Linux 32-bit (Pre-Meltdown patch!)

- 堆和站的起始位置都是随机的，处于安全考虑，让攻击没那么容易发生。
- 内核空间处于

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208071637752.png" alt="image-20220807163736454" style="zoom: 80%;" />

# CS162 Lecture 14: Memory 2: Virtual Memory (Con't), Caching and TLBs

## Summary: Paging

- 这个图需要注意的两个点：
  - 左边虚拟内存逻辑上是连续的栈内存，但是在页表上是映射到了两块不连续的物理内存之上的
  - 这个页表有很多空项，这些空项其实完全可以说是没有用的。这也是一级页表的很大的缺点。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081511203.png" alt="image-20220808151156896" style="zoom:67%;" />

## How big do things get?

- 典型的页大小是4KB
- 32位地址，一个4KB的页表需要占用的内存空间：
  - 偏移量12位，剩余20位全是页号。
  - 假定每个表项4个字节，需要占用$2^{32}/2^{12} * 4 = 4MB$空间
  - 当机器还是32位的时候，16MB内存都已经很大了，所以一个进程的页表直接占去了四分之一的内存空间
  - 假定有100个进程，那么页表就总共占了400MB内存，而实际上的进程数远不止100个。
  - 这个问题有一个治标不治本的方法：提高单页的大小，比如将单页大小提高为16MB，则一个页表缩小为原来的四分之一。
    - 这种方法的主要问题在于，**大内存页会导致每页内的浪费，这被称为内部碎片（internal fragmentation）问题（因为浪费在分配单元内部）。因此，结果是应用程序会分配页，但只用每页的一小部分，而内存很快就会充满这些过大的页。**
- 64位地址，一个页表占用的内存空间：
  - 偏移量12位，剩余52位全是页号
  - 一个表项8字节，需要$2^{52} * 8B$，非常大的一个数字。
  - **而且这还仅仅是一个进程的页表**
- 这个问题的本质在于，我们并不需要长度为$2^{20}$甚至$2^{52}$的页表，那么完整的页表其中基本上全部是空的，只有其中的一小部分是映射到物理内存的。大部分的表项被映射到虚无。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081516324.png" alt="image-20220808151611006" style="zoom:67%;" />

## Page Table Discussion

- 好处：
  - 内存分配简单
  - 内存共享简单
- 缺点：
  - 地址空间稀疏的情况下大部分表项是空的。
  - 页表可能会非常大

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081545948.png" alt="image-20220808154540651" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208191635862.png" alt="image-20220819163516512" style="zoom:67%;" />

## Fix for sparse address space: The two-level page table

- 两级页表，每级页表都是1024（1K）表项。
- 一级页表的每一项看作是指向一个二级页表的指针，二级页表的每一项则给出虚拟页对应的物理页
- 两级页表总共可以有2^20个物理页
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081559234.png" alt="image-20220808155937927" style="zoom:67%;" />

### Example: x86 classic 32-bit address translation

- 一级页表这里不叫一级页表，而是叫Page Directory，页表目录，挺合适的名字，一级页表实际上是一个页表的目录。
- 一级页表的每一项叫PageDirectoryEntry,PDE，可以看做是一个指向一个（二级）页表的指针。
- 二级页表就是此前所说的那种普通的页表，页表项PTE是一个20比特的物理页地址（用于和偏移量拼接形成物理地址的那一部分）。
- CR3是一级页表PD的基址寄存器，改变CR3的值就改变了整个地址空间。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081616199.png" alt="image-20220808161619894" style="zoom:67%;" />

## What is in a Page Table Entry (PTE)?

- PTE的构成：
  - 指向下一级页表的指针，或者是指向实际的物理页
  - 权限位：读写权限、合法位等等
- x86的PTE：
  - 高20位是指针
  - 权限位：
    - P：是否合法，Valid
    - W：是否可以写，Writeable
    - U：这是一个用户页还是内核页（用户模式的进程是否可以访问该页）
    - PWT：涉及到Cache的写策略，是否是全写法。
    - PCD：页缓存是否禁用
    - A：页最近是否被访问过，通常由硬件初始设置，由软件重设置。
    - D：脏位，页是否最近被修改，软件设置，当被修改时重设置。
    - PS：？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208081629329.png" alt="image-20220808162951009" style="zoom:67%;" />

### Examples of how to use a PTE

- Invalid的PTE有多种可能性：
  - 地址空间的这个区域确实是不合法的
  - 它只是暂时不合法，它对应的页在硬盘中，从硬盘中加载之后，重置Valid Bit为合法即可
  - 最先检查合法性，在合法的情况下剩余的31位可以用于位置信息
- Demand Paging：需求分页，缺页中断
  - 只在内存中保存当前活跃的页，其余不活跃的页保存在硬盘中，将它们的PTE标记为非法
- Copy On Write：
  - Unix的`fork()`只复制了父进程的页表，而不是将完整的内存复制一遍。
  - 创建进程之初，父子进程实际上是共享同一块物理内存的的。
  - 将页表中的PTE标记为只读，当有写入行为时才触发Page Fault，复制出两份副本。
  - fork出的子进程共享父进程的物理空间，当父子进程**有内存写入操作时**，read-only内存页发生中断，**将触发的异常的内存页复制一份**(其余的页还是共享父进程的)。
  - fork出的子进程功能实现和父进程是一样的。如果有需要，我们会用`exec()`把当前进程映像替换成新的进程文件，完成自己想要实现的功能

- Zero Fill On Demand：
  - 讲的不是很详细，em

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091512026.png" alt="image-20220809151205672" style="zoom:67%;" />

## Sharing with multilevel page tables

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091533095.png" alt="image-20220809153353792" style="zoom:67%;" />

## Summary: Two-Level Paging

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208201442492.png" alt="image-20220820144254216" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208201445517.png" alt="image-20220820144553237" style="zoom:67%;" />

- 最好的情况下，页表的总尺寸大约和被程序的虚拟内存用到的页数相等。因为假如我们的地址空间中有一大块的未映射空间，那么我们只需要在最顶级的页表的对应页表项设置一个NULL，然后我们甚至就不需要有第二级页表。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091536553.png" alt="image-20220809153607967" style="zoom: 67%;" />

## Multi-level Translation: Segments + Pages

- 不是为进程的整个地址空间提供单个页表，而是为每个逻辑分段提供一个。我们可能有 3 个页表，地址空间的代码、堆和栈部分各有一个。

- 分段和分页结合，一级用分段，二级用页表。
- 虚拟地址分为段号+虚拟页号+偏移量。
- 一级的段指向二级的某一个页表，再根据虚拟页号得到对应的PTE，然后得到物理地址
  - 在分段中，有一个基址（base）寄存器，告诉我们每个段在物理内存中的位置，还有一个界限（bound）或限制（limit）寄存器，告诉我们该段的大小。在杂合方案中，我们仍然在 MMU 中拥有这些结构。在这里，我们使用基址不是指向段本身，而是保存该段的页表的**物理地址**。界限寄存器用于指示页表的结尾（即它有多少有效页）。
  - 每个Base寄存器（段基址寄存器）就相当于是一个页表基址寄存器
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208192243638.png" alt="image-20220819224312274" style="zoom:67%;" />

- 上下文切换的时候需要保存和重新加载的？
  - 顶级段寄存器的内容（绿色的表，也就是Base和Bound寄存器的内容，即页表的物理地址和界限）
  - 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091555187.png" alt="image-20220809155530885" style="zoom:67%;" />

## What about Sharing (Complete Segment)?

- 将AB两个进程的一级的段表的某一项（比如Base2）指向同一个页表即可。
  - 这个页表中的每一页都是由AB两个进程共享

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091600123.png" alt="image-20220809160023824" style="zoom:67%;" />

## Multi-level Translation Analysis

- 好处:
  - 页表的尺寸大大减少了。
  - 内存分配很简单，因为页的大小是固定的
  - 共享也很简单
- 坏处
  - 每页一个指针
  - 页表需要时连续的，但是32位的二级页表的情况下，每一级的单个页表大小和一页是一样的（4KB）。
  - 2次或多次的寻址，速度可能会慢一些。访问内存比访问CPU慢

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091619268.png" alt="image-20220809161944949" style="zoom:67%;" />

## Recall: Dual-Mode Operation

- 一个进程不能修改它自己的页表。否则它就可以访问全部的物理内存，保护性也就不再存在。
- 将修改页表的相关行为限制在内核模式之下才有了保护性。
- x86实际上有四种模式。大部分OS只使用其中两种
  - ring 0：内核模式
  - ring3：用户模式
  - CPL：Current Privilege Level
  - 支持虚拟机的模式：Ring -1
- 特定的行为仅限于内核模式：
  - 修改页表基址（CR3）
  - 修改页表本身

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202208091627796.png" alt="image-20220809162725489" style="zoom:67%;" />

## X86 Segment Descriptors (32-bit Protected Mode)

- 指令中是隐含段的（什么一地址指令，二地址指令）
- 总共有6个段寄存器：StaticSegment，CodeS，DataS，ES，FS，GS。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031352159.png" alt="image-20220903135208765" style="zoom:67%;" />

## X86_64: Four-level page table!

- X8664的四级页表。没什么好说的，就举了一个例子
- 级数越多，地址翻译的开销就越大
- 总共48位的虚拟地址，每个页面是4KB，则偏移量部分是12位。剩余的36位就均分成等大的4级，每级就是9位的索引。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031424296.png" alt="image-20220903142400983" style="zoom:67%;" />

### From x86_64 architecture specification

- 当前所有的x86处理器都支持64位运算
- 字长64位，**但是地址只有48位**。

### Larger page sizes supported as well

- 可以通过取消更高级数来增加偏移量，从而得到更大的物理分页尺寸。

- 比如对于上述的四级页表，取消掉第四级，那么偏移量就有12+9位，单个页面的尺寸就大了很多。
- **当然，更大的页面尺寸也会存在内部碎片的问题。**并不是一个可忽略的缺陷。
- 有一定的实用价值，可以用于对内存需求大的情况下，这样可以减少分页。

### IA64: 64bit addresses: Six-level page table?!?

- 访问的开销太大了，太慢了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031440541.png" alt="image-20220903144009257" style="zoom:67%;" />

## Alternative: Inverted Page Table

- 反向页表。**反向页表只有一个，而不像正向页表每个进程一个**
- 此前的页表都是正向页表，每个虚拟地址一个表项。
- 而反向页表，则是每个物理页一个表项，因此表项的数目相较于正向页表会极大的减少。
- 正向页表的尺寸基本上是和分配各对应的进程的内存大小正相关的，分配的内存越大，页表越大。
- 而反向页表的尺寸是与虚拟地址空间无关的，因为在内存一定的情况下，物理页的页数是固定的。
  - 反向页表的尺寸和内存的大小是正相关的。因为内存越大，分页的页数就越多。
- 在反向页表（inverted page table）中，可以看到页表世界中更极端的空间节省。在这里我们保留了一个页表，其中的项代表系统的每个物理页，而不是有许多页表（系统的每个进程一个）。页表项告诉我们哪个进程正在使用此页，以及该进程的哪个虚拟页映射到此物理页。现在，要找到正确的项，就是要搜索这个数据结构。线性扫描是昂贵的，因此通常在此基础结构上建立散列表，以加速查找。
- 复杂的地方就是这个哈希表了，一般用硬件。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031458302.png" alt="image-20220903145830950" style="zoom:67%;" />

## Where and What is the MMU ?

- MMU，内存管理单元，位于CPU和内存的缓存之间，负责将虚拟地址转换为物理地址。

<img src="C:/Users/jiangyiqing/AppData/Roaming/Typora/typora-user-images/image-20220903152057225.png" alt="image-20220903152057225" style="zoom:67%;" />

## Translation Look-Aside Buffer

- TLB
- 记录最近使用的虚拟页号和其对应的物理页号的映射。
- 如果TLB命中，就不需要去访问物理内存中的页表。直接使用缓存的物理页号与虚拟地址中的偏移量拼接即可得到物理地址。
- 否则就需要去访问物理内存中的页表。
- **TLB也可以有多级，TLB也是缓存的一种，缓存可以有多级缓存，现代处理器确实有多级TLB**
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031537335.png" alt="image-20220903153750050" style="zoom:67%;" />

## A Summary on Sources of Cache Misses

- 缓存未命中的类型

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031556628.png" alt="image-20220903155640316" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031557567.png" alt="image-20220903155715267" style="zoom:67%;" />

# CS162 Lecture 15: Memory 3: Caching and TLBs (Con't), Demand Paging

- 首先复习了一下Cache的三种存储方式
  - 全相联、组相联、直接映射
- Cache的写入方式
  - 写回法和全写法
- 如何根据地址从Cache找到对应的块

## Physically-Indexed VS Virtually-Indexed Caches

- 使用物理地址索引的缓存和使用虚拟地址索引的缓存。
- 最常见的是使用物理地址索引的缓存。
  - CPU给出虚拟地址，然后（优先）通过TLB或者查询页表得到物理地址，然后通过物理地址来索引高速缓存
  - 也就是说地址是在经过翻译后提交给缓存的。
  - 好处是在进行上下文切换的时候不需要清空缓存，因为缓存是物理地址索引的，一个进程不可能访问另外一个进程的物理地址。
    - 比如进程A缓存了物理地址P1的数据在缓存中，进程B不可能访问得了这个缓存，因为它没办法得到物理地址P1。
  - 坏处是，TLB在访存的过程中起到关键性的作用，如果TLB快，那么访存就快。
- 虚拟地址索引的缓存：
  - 地址在经过翻译之前就递交给缓存。
  - 好处是TLB不在具有重大影响，因为直接越过了TLB的翻译过程。
  - 坏处：在进行上下文切换的时候需要清空缓存以防止进程之间相互读取数据。
    - 进程A为虚拟地址1缓存了数据在Cache中，进程B想访问它的虚拟地址1的数据，如果没清空Cache的话，访问到的是A所缓存的数据。
- 目前我们都仅限于物理地址索引的缓存。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031848121.png" alt="image-20220903184824688" style="zoom: 50%;" />

## TLB organization: include protection

- 小的TLB一般是全相联缓存。使用虚拟地址索引，返回物理地址以及其他的信息。
- 太慢的时候使用TLB切片。在前端直接放置几个直接映射的缓存

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031906611.png" alt="image-20220903190615303" style="zoom:67%;" />

## Reducing translation time for physically-indexed caches

- 从逻辑上来说，要先通过TLB得到物理地址，才能去查询缓存。
- 但是为了加速，实际上可以将TLB访问和Cache访问并行。基于偏移量可以比物理页号更早一步得到。
- 而偏移量恰好覆盖了查询缓存所需要的cache index和byte select。
- 因此可以在查找TLB的同时去借助cache index和byte select查找Cache。即使逻辑上二者是串行的。

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031910726.png" alt="image-20220903191054294" style="zoom:67%;" />

## Current Example: Memory Hierarchy

- 和缓存一样，TLB也有多级缓存，当一级TLB未命中时，去查询二级TLB，二级TLB未命中时才会去查询页表。
- TLB既有指令TLB，取指令专用，也有数据TLB。二级TLB是两个一级TLB共享的，SharedTLB(STLB)。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031926656.png" alt="image-20220903192601334" style="zoom:67%;" />

## What happens on a Context Switch?

- 上下文切换的时候，肯定需要对TLB做一些工作的，毕竟TLB是页表的缓存，而页表是虚拟地址到物理地址的映射关系。
  - 地址空间改变，TLB项也就不再合法。
- 做法：
  - 完全清空TLB，这样就保证不会存在老的进程的缓存项。但是如果上下文切换频繁，开销就会很大。
    - 每次进程运行，当它访问数据和代码页时，都会触发 TLB 强制未命中（因为TLB最开始是空的啊）。如果操作系统频繁地切换进程，这种开销会很高
  - 给TLB的每一项在硬件增加一部分东西，用来记录这一项映射关系对应的进程的PID。
    - 当前进程会忽略那些PID与它自己的PID对不上的TLB缓存项。
    - 因此也就需要一个寄存器来保存当前进程的PID以供对比，在发生上下文切换的时候也要修改这个寄存器的值为新的PID

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031935643.png" alt="image-20220903193510326" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031942946.png" alt="image-20220903194245621" style="zoom:67%;" />

## Putting Everything Together

- 现在我们将我们的访问内存的模型完善，将二级页表、TLB、Cache联合在一起。
- 通过二级页表查询：
  - 虚拟地址P1索引到一级页表目录的某一项，在其指向的二级页表中利用虚拟地址索引P2找到对应的PTE，然后得到物理页号，与偏移量拼接得到物理地址。
  - 虚拟地址索引P1和P2联合起来作为索引从TLB查找到对应的项，然后得到物理地址
  - 得到物理地址之后，先查找Cache，未命中再查找物理内存。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209031950691.png" alt="image-20220903195059372" style="zoom:67%;" />

## Page Fault

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209131954955.png" alt="image-20220913195420673" style="zoom:67%;" />

- 虚拟地址到物理地址的翻译有时候会失败。
  - 这种失败有多种原因。
    - PTE的合法位被标记为不合法、对被标记为只读的页面进行写操作、访问隔离、或者页根本不存在（Swap，比如）
  - 会导致一个Fault/Trap。
    - 不是一个中断，因为中断是非同步的，而PageFault是同步的。
      - 中断和异常：
      - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209131958216.png" alt="image-20220704212049357" style="zoom: 33%;" />
  - 可能会在获取指令或者数据的时候出现（读取内存某一页）
- 操作系统会介入PageFault，试图挽回情况并且重试刚才引起PageFault的指令。
  - 比如分配额外的页
  - 比如将页从硬盘拉进内存
  - 比如将页由只读变为读写。CopyOnWrite就是一个很好地例子。
- PageFault是一个硬件与软件之间的限制关系的翻转。
  - 通常来说，软件基于硬件运行，而PageFault则是硬件无法继续运行直到软件运行。
- **PageFault和SegmentationFault的区别在哪里？**
  - PageFault：程序试图访问一个处于页表中的内存页，但是它当前不被允许访问。于是接下来操作系统得到一个PageFault，作为结果操作系统可以去做一些工作，试图修复这种情况。仅仅是出现一个PageFault并不意味着程序会终止，出现PageFault并不是致命性的。
  - SegmentationFault：程序试图访问不属于分配给它的内存，这个之所以叫做段错误是因为历史原因（内存分段嘛），一般来说出现SegmentationFault，程序会被操作系统直接终止，出现SegmentationFault一般是致命性的。
  - SegmentationFault的出现一定先于PageFault，因为检查地址是否越界是地址翻译过程中的第一个检查。
    - 或者说，对于地址是否越界的检查是先于对于页是否合法的检查的。

## Demand Paging

- 按需分页：根据程序的实际需要去分配物理页。
- 现代的程序通常需要很多的物理内存，因为可能有上百万条的机器指令需要载入内存。
  - 但是根据二八定律，百分之八十的机器指令不会被经常使用，只有其中的百分之二十的指令是会被经常访问的
    - 或者说，程序的百分之八十的时间是在访问那百分之二十的指令。
  - 所以如果无脑一次性将程序的所有的机器指令都载入内存，会造成极大的内存浪费
    - 比如一百万条指令，可能其中有五十万条直到程序正常结束都没被CPU访问过一次，直接浪费一半的内存。
  - 所以这就引出了按需分页：将内存作为磁盘的“缓存”，将经常使用到的指令缓存在内存中，而大部分的指令还处于硬盘中。
  - 于是只有那些被使用到的指令会占据内存，而那些从来没被使用过的指令就完全不会占据内存。
    - 这样看上去就既有了内存的高速，又有了磁盘的大容量。
    - **缓存总是用来做这样的工作的**
- 具体策略：举一个例子
  - CPU取第一条指令，取到了，无事发生。
  - CPU取第二条指令，展开细说：
    - CPU给出第二条指令的虚拟地址
    - 得到虚拟地址之后MMU进行地址的翻译，然后MMU发现其对应的PTE是不合法的。
    - 于是操作系统得到一个PageFault
    - PageFault Handler修复情况，从硬盘的某个位置把对应的页拉取进入内存，然后更新PTE表项，将其标记为合法
    - CPU重试刚才触发PageFault的指令。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209132039886.png" alt="image-20220913203911554" style="zoom:67%;" />

### Demand Paging as Caching, …

- 由于我们是将内存看做了硬盘的缓存，所以Cache相对于Memory的那些问题，在Memory相对于Disk时也存在。
- “块的大小”？每页4KB
- 如何组织缓存的映射关系？全相联，直接映射，etc
- 页替换的策略是什么（毕竟缓存是有限的，而指令的数量却可以是相对无限的）？LRU？随机？
- 缓存未命中时怎么办？去更低一级的存储介质找，比如硬盘
- 当有写入时怎么办？（是同时写回硬盘和内存（全写法），还是在被替换出去的时候再写回硬盘（写回法）？）
  - 绝对不会采用全写法，因为写回硬盘的速度实在是太慢了，和写入内存的速度相差太多了
  - 所以采用写回法，于是就需要脏位（位于PTE中）来追踪对应的块是否被修改过了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209132055258.png" alt="image-20220913205551974" style="zoom:67%;" />

### Review: What is in a PTE?

- PTE中除了有物理页的页号以外，还有：
  - 脏位，用于追踪这个PTE对应的物理页是否被修改过
  - 访问位，用于追踪这个PTE对应的物理页是否被访问过
  - 权限位（U）：这一页是属于内核的还是属于用户的
  - 其余的看图，还有比如记录该页的读写权限的，是读写还是只读还是只写，再比如如果是多级页表，还有指向下一级的指针。
  - 记录物理页号的那些比特，在缺页时也会用于记录页面在硬盘中的位置（哪个扇区，哪个磁道，这种）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209132120214.png" alt="image-20220913212012916" style="zoom:67%;" />

### Demand Paging Mechanisms

- PTE使得按需分页是可实现的。
  - Valid：页在物理内存中，不会触发PageFault
  - NOT Valid：页不在物理内存中，触发PageFault，使用PTE中的相关信息，找到硬盘中的对应位置，然后把页从硬盘拉进内存。
- 当用户引用了带有非法位的PTE时？
  - MMU陷入OS，导致PageFault。
  - OS在处理PageFault时会做什么？
    - 挑选一个老的页用于替换掉。具体的算法诸如LRU，Random。
    - 如果被选中的老的页被修改过（脏位为“1”），将其内容写回硬盘。（如果没被修改就不需要写回硬盘，因为和硬盘里的是一样的）
    - 改变老的页的PTE以及任何被缓存的TLB项。因为页被替换了，肯定要标记对应的PTE和缓存为**invalid**
    - 将从磁盘找到的新的页载入内存（也就是直接覆盖掉刚才选中的老的页）
    - 更新页表项，作废以前的TLB
    - 从触发PageFault的地方继续执行

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209132124915.png" alt="image-20220913212419626" style="zoom:67%;" />

## Classic: Loading an executable into memory

- 经典且少见的情况：OS将一个整个可执行文件载入内存，初始化寄存器和栈指针。
  - 应该都是按需分页的形式了？
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209141607548.png" alt="image-20220914160730182" style="zoom:67%;" />

## Create Virtual Address Space of the Process

- VAS：Virtual Address Space
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209141611560.png" alt="image-20220914161116267" style="zoom:67%;" />

# CS162 Lecture 16: Memory 4: Demand Paging Policies

## Working Set Model

- 一个程序随着时间运行，每一个时刻中被用到的地址都不是完全一样的，在某一个时刻被这个进程所用到的那些虚拟地址，就是这个进程的工作集。
  - 工作集是进程的地址空间的子集，并且随着时间在不断发生变化
  - 从图形上表示，就是下图，取横轴`Time`某个时刻，向上画一条与纵轴`Address`平行的线，与线相交的蓝色部分就是这个时刻的工作集。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209141730050.png" alt="image-20220914173055759" style="zoom:67%;" />

## Demand Paging Cost Model

- 既然DamadPaging就像是Cache一样，自然也会和Cache一样有`Cache Miss`和`Cache Hit`。所以也就可以去计算DamandPaging的平均访问时间，在这里称作`EffectiveAccessTime,EAT`
- 和计算Cache的平均访问时间一样，平均访问时间 = 命中率 * 命中的访问时间 + 未命中率 * 未命中访问时间。
  - 而未命中访问时间 = 命中访问时间 + 访问更低一级存储的时间（也就是缓存未命中惩罚）。
- 举例：
  - 内存访问时间200$ns$
  - 平均的PageFault服务时间8$ms$
  - 假定缓存未命中率为$P$，则缓存命中率为$1 - P$
  - 于是，$EAT = 200ns * (1-p) + (200ns + 8ms)*p = 200ns + 8ms * p = 200ns + 8*10^6 * p ns$
  - 如果每一千次内存访问会出现一次PageFault，那么$EAT = 8.2 \mu s = 8200 ns$，平均的访问时间增大了四十倍。
  - 如果想让平均访问时间约为内存访问时间的$110\%$，那么大概是每四十万次内存访问出现一次PageFault。
- 所以通过举例我们可以知道，PageFault的代价其实是非常大的（因为访问磁盘实在是太慢了），所以要尽量避免触发PageFault。
- 所以不触发PageFault是极端重要的，这意味着我们需要极力去确保目前正处于内存中的页是正确的页（我们需要的页），从而避免未命中。
  - 并且，如果我们有正确的页处于内存中，我们也要极力避免把不该替换出去的页替换掉（比如踢出去了访问最频繁的页），于是这就要求我们需要一个良好的替换算法。
  - 如果某个进程需要分配一页，我们不会想丢掉对我们有用的页，因为那会导致我们又得重新去访问磁盘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209141800402.png" alt="image-20220914180040092" style="zoom:67%;" />

## What Factors Lead to Misses in Page Cache?

- 强制未命中：因为最开始缓存是空的，所以第一次肯定是未命中的。
  - 此前从未被分页到内存里的页，就会发生未命中，触发PageFault。
  - 如何避免强制未命中？
    - 预取：提前预知未来将有哪些页会用到，然后提前取出来。
    - 需要对访问内存的行为模式进行观察和预测。
- 容量未命中。缓存的容量不够，旧的已缓存项被新的缓存项挤出了缓存，旧的缓存项就会未命中。
  - 多买几根内存条来提升内存容量。
  - 或者：如果是多个进程在内存中运行，调整分配给每个进程的内存比例。
- 冲突未命中。理论上在虚拟内存上不存在。因为这是一个全相联映射
- 策略未命中。由于替换策略导致的未命中。
  - 页曾经处于内存中，但是由于替换策略被暂时踢出了内存。
  - 如何解决？更好的替换策略。
  - 我们应该尽力避免策略未命中，因为这会导致不必要的访问硬盘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209142029160.png" alt="image-20220914201845216" style="zoom:67%;" />

## Page Replacement Policies

- 为什么我们这么关心分页的替换策略？
  - 首先DamandPaging是一种缓存，凡是缓存都需要考虑替换策略
  - 其次好的替换策略对于分页来说尤其重要。
    - 对于Cache和DRAM来说，未命中的惩罚相对Disk来说小得多，因为DRAM的访问速度仅次于Cache。
    - 而对于Disk和Memory的缓存体系来说，未命中的惩罚极大，因为访问Disk的速度实在是太慢了，相当于几百万条指令的时间。
- FIFO：没什么好说的，先进先出。
  - 丢弃最久远的页。公平策略，每一页在内存中生存的时间都是一样的。
  - 不是一个很好的策略，因为极大可能会抛弃最久远但是也是使用最频繁的那一页。
- RANDOM：随机策略。
  - 随机选择一个页进行替换。
  - 对TLB可能是一个典型策略，因为TLB本身也快，未命中惩罚也相比分页不重，然后可以简化硬件？
  - 但是对分页就不太行，随机选的话很可能选到不该选的。
- MIN：替换掉在最远的未来才会被用到的页。谁在未来最后被用到，谁就被替换掉。
  - 可以证明是最优的替换策略，但是没人能真的预测未来。
  - 所以只能根据过去来推测未来。
  - LRU就是MIN策略的一个模拟策略。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209142055767.png" alt="image-20220914205544463" style="zoom:67%;" />

## Replacement Policies (Con’t)

- 如何实现LRU？
  - 一个链表，`Head`指向最近最新使用过的页，`Tail`指向最近最少使用的页。
  - 每当需要替换时，移除`Tail`指向的节点，同时让`Head`指向新的头结点（也就是刚刚使用的页）
- 问题：每次有内存读写，都要求调整LRUList，而调整LRUList又需要多次内存读写（因为要重新调整头和尾）
  - 也就是说，为了实现LRU，每次内存读写实际上都要求多次内存读写。
- 实践中，是模拟LRU。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209142216082.png" alt="image-20220914221617775" style="zoom:67%;" />

## Example: FIFO (strawman)

- 一个FIFO的模拟罢了，没什么好说的
- 在用D替换进去的时候，选择A是一个很差的选择，因为下一个即将被访问的就是A。这就是FIFO可能会出现的缺陷
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209142303133.png" alt="image-20220914230332814" style="zoom:67%;" />

## Example: MIN / LRU

- 使用MIN策略的模拟，并且在这个访问次序之下，LRU所做出的决策和MIN所做出的决策是一模一样的。
  - 具体指的就是在用Ｄ替换Ｃ时，LRU会做出一样的选择。
  - 所以说LRU可以模拟MIN。
  - **但LRU并不是永远完全符合MIN的**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209142304513.png" alt="image-20220914230430216" style="zoom:67%;" />

## When will LRU perform badly?

- 在下面的访问次序之下，LRU的表现和FIFO一模一样，并且表现极差，每次内存访问都会触发PageFault
- MIN的表现则更好。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209142327417.png" alt="image-20220914232746102" style="zoom:67%;" />

## Graph of Page Faults Versus The Number of Frames

- 一般来说人们期望命中率随着物理内存的增加而逐渐下降，但这个现象并不是总会发生的。
  - 可以看到，一直增加物理内存的容量，并不会一直减少未命中率，未命中率随着内存的增加而递减，逐渐趋于稳定(然后可能会继续下降)。
- 对于特定的页面替换算法(FIFO)来说，增加物理内存反倒可能使得未命中率增大。

- 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151334591.png" alt="image-20220915133404191" style="zoom:67%;" />

## Adding Memory Doesn’t Always Help Fault Rate

- 对于LRU和MIN来说，增加内存有助于减小未命中率。
  - 未命中率可能先递减，然后趋于稳定在一个常数附近，然后再递减。
  - 不管怎么样，增加内存最坏的结果就是未命中率不变，至少不会使得未命中率增加。
  - 因为不管如何，增加内存之前的内容，一定是增加内存之后的内容的一个子集，这就决定了未命中率至少不会下降。
  - 
- 而FIFO，增加内存反而可能会使得未命中率上升。
  - 所以一般来说，弃用FIFO。
  - 
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151343023.png" alt="image-20220915134331613" style="zoom:67%;" />

## Approximating LRU: Clock Algorithm

- 复习一下PTE的结构先：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151503044.png" alt="image-20220915150354733" style="zoom: 67%;" />

- 模拟LRU的时钟算法（MIN的模拟的模拟）
  - 替换一个老的页面，**不是最老的页面**
- 细节：
  - 将所有的物理页组成一个环，一个时钟。
  - 有一个时钟的指针指向环上的某一个物理页面。
  - 在每个物理页对应的PTE或者TLB中，有一个`Usebit`用于标记这个页面是否最近被使用过。
  - `Usebit`由硬件设置为1，而由OS设置为0。每当硬件使用某一页物理页时，硬件将Usebit设置为1。**硬件永远不会将其置为0**。
  - **当且仅当发生PageFault时**，指针向前移动，直到寻找到一个`Usebit`为0的页为止，在这个寻找的途中，指针所遇到的所有的`Usebit`为1的页，都会被置为`Usebit` = 0
    - 指针向前移，然后遇到第一页，第一页`Usebit = 1`，意味着上一次指针指向它时它被使用过，将其置为0，然后指针继续前移。
    - 一个一个检查，直到找到`Usebit`为0的一页，然后选定它替换。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151410232.png" alt="image-20220915141010845" style="zoom:67%;" />

- 是否会出现指针永远循环下去，一直找不到可以替换的页的情况？
  - 不会，因为在运行这个算法时，所有的其他进程都是挂起的，只有OS在运行
  - 最坏的情况也就是指针转了一圈又回到最初的起点，然后找到刚才第一个被置为0的页
- 如果指针转的很慢？是好事还是坏事？
  - 指针只有在发生了PageFault的时候才会移动，所以指针转得慢说明PageFault出现的频率不高，或者很快就寻找到了合适的页。
- 如果指针转的很快，则恰好相反，很多PageFault，或者很多Usebit被置为0。不管哪一种都说明目前内存压力较大。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151445998.png" alt="image-20220915144521626" style="zoom:67%;" />

## Nth Chance version of Clock Algorithm

- 每个页面除了`Usebit`以外，还有一个计数器，记录其在Usebit为0时，被指针扫过但是没有被选中（也就是说被放过一次）的次数。
- 在PageFault时，OS检查Usebit：
  - 如果Usebit为1，说明其近期被使用过，将其置为0，同时也将计数器置为0。
  - 如果Usebit为0，则计数器自加1，如果计数器的值到达选定的临界值N，则选中这个页面替换掉。（已经放过你N次了，这次不能放过你了）
  - 这意味着指针在替换一个**一直没被**使用过的页面之前，必须先扫过它N次（放过它N次）
- 如何选择N？
  - 较大的N，更好的模拟LRU，如果N在1-1K之间，模拟很好
  - 较小的N，效率更高，因为N越大，需要放过某个未使用页的次数就越多，找到可以被替换的页的耗时就越长。

- 如果选中的页恰好是此前被修改过的页？（脏位为1，脏页）
  - 因为替换一个脏页需要将这个页回写到硬盘，所以替换它需要额外的开销，于是我们给它额外的一次机会，让它得以在被替换前完成回写工作。
  - 常见的方法：
    - 干净的页（未被修改过）：取N = 1。
    - 脏页：取N = 2，这样前一次放过它，它趁第一轮转圈的这个间隙内把内容写回硬盘，第二轮转圈指向它的时候就可以直接替换它了。
      - N = 1时回写回硬盘，N = 2时替换掉它。
      - 不用担心写回硬盘之后，页上的内容又被修改了，因为除了OS以外的其他进程都是挂起的，不存在修改页的可能。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151452479.png" alt="image-20220915145211130" style="zoom:67%;" />

## Clock Algorithms Variations

- 我们是否真的需要一个硬件支持的脏位(modified bit)？
  - 可以不需要，但是需要软件形式的脏位支持。
  - 具体算法：
    - 初始时刻，将所有的页标记为只读，即使是可以写入的页。同时将软件形式的脏位全部置为0（页未被修改）
    - 对某一页的写入行为会导致PageFault，假定写入实际上是允许的，那么OS会将软件形式的脏位（这一页的）设置为1，然后同时将其标记为可写的页（在PTE标记）。
    - 无论何时将页写回硬盘时，将脏位清零，同时将页重新标记为只读。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151514313.png" alt="image-20220915151452009" style="zoom:67%;" />



- 我们是否真的需要一个硬件支持的Usebit？
  - 可以按前一个问题相似的方法来处理。
  - 算法：
    - 将所有的页标记为不合法，即使是那些已经处于内存中的。
    - 将Usebit和Dirtybit置为0.
    - 对非法页的读写将会陷入OS，从而告知页已经被使用过。
    - OS将软件形式的Usebit设置为1，来预示页已经被使用过。
      - 如果是读，将页标记为只读
      - 如果是写，将页标记为脏，同时标记为可写的。
    - 当时钟算法的指针经过，将Usebit重载为0，标记页面为不合法。
    - 注意脏位没有被改变，直到页面被写回硬盘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151515419.png" alt="image-20220915151506121" style="zoom:67%;" />

- **意义在于，有的架构是没有硬件支持的脏位和Usebit的**

## Second-Chance List Algorithm (VAX/VMS)

## Free List

- 时钟算法在后台负责填充FreeList。进程从FreeList的头部取页使用。
- 当填充进入FreeList的页是脏页时，将其写回硬盘。
  - 进程只会从FreeList的头部取一页来用，所以脏页可以在自己成为头部之前完成写回硬盘的工作，变为干净的页。
- 优点：PageFault处理更快

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151714484.png" alt="image-20220915171435166" style="zoom:67%;" />

## Reverse Page Mapping (Sometimes called “Coremap”)

- 一个反向的页表。
- 不是记录进程使用了哪些物理页，而是记录物理页被哪些进程所使用。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202209151719726.png" alt="image-20220915171922428" style="zoom:67%;" />

# CS162 Lecture 17: Demand Paging (Finished), General I/O, Storage Devices

## Allocation of Page Frames (Memory Pages)

我们如何在不同的进程之间分配内存？

- 是每个进程拥有同等份额的内存，还是动态的给每个进程分配不同的内存份额？同等份额带来的问题就是有的进程可能只会反复访问同样的1GB内存，但是它却被分配了10GB内存，而这10GB本来可以被其他有需要的进程使用。
- 我们是否应该将整个进程都从内存交换出去？
- 每个进程都需要有一个最小的页数，来保证程序能够正常执（例如，保存当前正在访问的机器指令的页）。
  - 或者说，我们需要它的工作集在内存中。

两种不同的页替换范围：

- 一种是全局范围内的替换。也就是说在全局范围内使用页的替换算法，当一个进程想要Page In Something的时候，它从所有的物理帧中选定一帧。一个进程可能会从另外一个进程抽取一帧。
- 另外一种是在本地的替换。我们给每个进程分配一定量的物理内存帧，然后当进程内存不足想要Page In Something的时候，它从自己所拥有的分配给它的物理帧中选定一帧来替换。这样可以达到公平的目的，或者用于保证实时系统的实时性。在这种情况下我们就需要一个用于给进程分配内存的策略。

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121412984.png" alt="image-20221112141229647" style="zoom:67%;" />

### Fixed/Priority Allocation

固定的分配策略。

- 等比例分配：每个进程得到相同比例的内存。
- 成比例分配：根据进程的尺寸来给进程分配内存。
  - 计算的流程如下：
  -  $s_i$ = size of process p_i and $S=\sum s_i$ 
  - $m$ = total number of physical frames in the system
  -  $a_i = $(allocation for p_i) $=\frac{s_i}{S}×m$。
  - 按照进程的大小成比例分配的问题在于，进程的大小并不能正确的反映一个进程所需要的内存的大小。进程的大小直接相关于代码的大小，当一个程序运行时，我们可能会链接很多功能强大的动态库，但是我们只会用到其中很少的一部分功能，这时候代码的大小和我们实际上会用到的代码之间就差别巨大，我们可能有一个很大的进程但是其实只使用了其中很少一部分的代码。
- 按照优先级成比例分配：使用优先级而不是进程的尺寸来对内存进行分配。
  - 计算的流程和上一个策略是一致的，只需要将尺寸换成优先级即可。
  - 如果某个进程发出一个`page fault`，那它就可能会从比它优先级低的所有进程中选定一帧进行替换。

实际上这些策略都是固定的，我们试图基于进程的某些静态属性来进行内存分配。但是有些进程就是需要更多的内存，有些进程就是不需要那么多内存，什么可能是一个进程需要更多内存的信号？`PageFault`。

- 出现大量`PageFault`的进程毫无疑问是需要更多内存的，什么进程需要更少的内存？不是`No PageFault`，而是`low PageFault`。

- 相对来说出现更多`PageFault`的进程是更需要更多内存的。也许我们可以基于这个事实来设计分配策略。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121427710.png" alt="image-20221112142727318" style="zoom:67%;" />

### Page-Fault Frequency Allocation

- 依据发生`PageFault`的频率来做内存分配。
- 我们是否可以通过动态的改变帧的数量和应用的数量来减少容量未命中？这就是我们现在要解决的问题。
- 我们有一个`PageFault`率的上限，和一个`PageFault`率的下限。
  - 进程的`PageFault`频率高于上限，就给它更多的物理帧。
  - 进程的`PageFault`频率低于下限，这意味着我们可以回收它的某些内存而不造成明显影响，于是我们就回收它的某些帧。
  - 我们希望我们的进程的`PageFault`频率处于上限和下限之间，这样内存能够得到有效的利用。
- 如果我们的内存就是不够呢？最有效的办法肯定是加内存。但是得加钱，没钱怎么办？
  - 我们把某些进程给完全Swap Out到硬盘里去，腾出一部分的内存空间，保留在内存中的那些进程就可以运行得快一些，然后等这些进程结束了，我们再把硬盘里的进程Swap In Back。
  - 因为当`PageFault Rate`处于上限之上的时候，OS唯一在做的事情就是不停的`Swap In And Swap Out`。如果我们把某些进程给丢到硬盘里去，其他进程的`PageFault Rate`就可以降下来，降到合理区间之后它们就会运行的更高效，等这些进程结束之后，我们再把丢进硬盘的进程交换回来。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121457856.png" alt="image-20221112145736541" style="zoom:67%;" />

### Thrashing(抖动，颠簸)

- 抖动：一个进程忙于交换页进来和出去，程序没有进展。
- 抖动通常发生在内存不足（页不足）的情况下。
- 查看右侧的图，会发现当线程的数量在一个合适的数量的时候，CPU的利用率会上升。因为我们减少了在I/O上的阻塞时间，一个线程因为I/O阻塞时，总有其他的线程来替代它。
- 而当线程的数量达到了抖动的临界点的时候，线程的数量过多，CPU忙于从硬盘读写（Swap In & Out），CPU的利用率开始下降。
  - 更多的线程导致更多的Paging的原因是，它们会有更多的单独的内存请求（比如每个线程都有一个栈）。
- 如何应对抖动：暂停某些进程，将它们丢入硬盘，让留在内存的进程做一些进展。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121511849.png" alt="image-20221112151135503" style="zoom:67%;" />

### Locality In A Memory-Reference Pattern & Working-Set Model

- 工作集的概念：给定时间内被访问到的物理页的集合。
  - 这个给定时间不能太小，也不能太大。太小我们看不出规律，无限大那就是把整个进程访问过的所有页都包含进来，无意义。
- 工作集定义了一个进程正常进展所需要的最小页数。
- 当内存不足以容纳所有进程的工作集的时候，就会发生抖动。
  - 当我们把所有进程的工作集加起来，大于内存容量时，我们就认为会发生抖动。
  - 应对措施：Swap

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121528962.png" alt="image-20221112152803606" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121530507.png" alt="image-20221112153005171" style="zoom:67%;" />

## What about Compulsory Misses?

- 强制未命中：第一次访问某页的时候的未命中，因为最开始缓存是空的。
  - 可以发生在第一次访问某一页。
  - 也可以是在进程被Swap Out，然后重新Swap In的时候发生。
- Clustering：
  - 在`PageFault`的时候，除了把发生`PageFault`的那一页载入内存，也将其附近的页一同载入内存。因为硬盘的连续读取是最快的，所以连续读取多个页是有意义的。
- 工作集追踪：使用某种办法追踪应用的工作集，当将进程Swap Back In的时候，将其工作集Swap In。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121553861.png" alt="image-20221112155302541" style="zoom:67%;" />

## Meltdown

- 介绍了一下在Meltdown攻击之前和之后，OS的地址空间的不同了。
- Meltdown攻击之后，每个进程两个页表，一个管理用户态空间，另外一个管理内核态的空间。

## I/O

讲了一些I/O设备什么的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121819762.png" alt="image-20221112181941413" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121819951.png" alt="image-20221112181958610" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121820507.png" alt="image-20221112182018158" style="zoom:67%;" />

# CS162 Lecture 18: General I/O (Con't), Storage Devices, Performance

## Transferring Data To/From Controller

- 编程的I/O：CPU参与每一个字节的传输。
  - 好处：硬件简单，编程简单
  - 坏处：消耗与数据大小成正比的CPU时间。
- DMA：CPU告诉控制器将数据从哪里传输到哪里，给予它内存总线的访问权，将数据传输的工作交给DMA，DMA就扮演编程的I/O中的CPU的角色。在传输完成之后通过中断告知CPU。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121917387.png" alt="image-20221112191709017" style="zoom:67%;" />

## I/O Device Notifying the OS

I/O设备通知OS

- I/O中断：
  - 设备在需要服务的时候生成一个中断
  - 好处：处理不规律的事件能够处理的很好，因为直到中断出现的时候才会有开销。
  - 坏处：中断的开销相对高。因为要转移到内核，需要做栈保存栈切换寄存器保存之类的工作。
- 轮询：
  - OS周期性地检查特定于设备的状态寄存器。
  - 好处：开销很低，因为我们只需要不断查看一个寄存器即可。
  - 坏处：对于请求不那么频繁的或者无规律的I/O来说，使用轮询可能会白白浪费大量的CPU时间。
- 实际的设备将轮询和中断结合。
  - 比如高带宽的网络适配器：
    - 第一个进来的包使用中断，然后关闭中断，使用轮询来接收随即到来的其他包，接收完毕后打开中断。
- OS来决定究竟是使用中断还是轮询。OS可能决定一直启用中断，然后直到第一个中断发生，关闭中断然后使用轮询来接收随即到来的包，也可能一直禁用中断。设备控制器一般两个都提供。

- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121924911.png" alt="image-20221112192409588" style="zoom:67%;" />

## Recall: Device Drivers

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121952343.png" alt="image-20221112195247012" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121952768.png" alt="image-20221112195259414" style="zoom:67%;" />

## The Goal of the I/O Subsystem

- 下面的代码可以在不同的设备上运行，就是因为I/O子系统提供了一个统一的接口。
  - 不管是什么样的I/O设备，fopen和fprintf都可以工作。
  - `/dev/`目录是Linux下各种I/O设备，比如我们可以通过这个目录打开一个针对键盘的`FILE`，然后获取键盘输入。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211121958245.png" alt="image-20221112195839923" style="zoom:67%;" />

## Want Standard Interfaces to Devices

将设备粗略地分为三类：

- Block Devices：硬盘、ssd、磁带之类的
  - 以块为单位传输和访问数据。
  - 由文件系统来实现以字节为单位的访问
- Character Devices：鼠标、键盘、串行端口、某些USB设备
  - 一次一个字符。
- 很少使用裸露的接口，一般都通过文件系统。
- Network Devices：以太网、蓝牙等等。
  - 既不是一次一个字符，也不是一次一个固定大小的块。包的大小是不固定的。所以单独一类。
  - 用法就像管道或者FIFOs或者流

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122006678.png" alt="image-20221112200617346" style="zoom:67%;" />

## How Does User Deal with Timing?

- 阻塞的，非阻塞的，异步的。非阻塞的情况下就可能需要不断轮询。异步：给OS一个缓冲区，告诉OS我要十个字节，然后立即返回，OS在数据准备好以后跟你说“你的数据好了”。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122019867.png" alt="image-20221112201954541" style="zoom:67%;" />

## Storage Devices

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122044654.png" alt="image-20221112204433328" style="zoom:67%;" />

## The Amazing Magnetic Disk

- 介绍了一下磁盘相关的各种知识。
- 每个磁道之间会有一些空白的区域，用于保证在对一个磁道写入时，不会破坏其相邻的磁道。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122045369.png" alt="image-20221112204529005" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122045480.png" alt="image-20221112204548143" style="zoom:67%;" />

### Shingled Magnetic Recording (SMR)

- SMR盘与典型的磁盘的不同之处在于，相邻的两个磁道之间有一半是相互重叠的。更紧密的磁道排列带来了更大的容量，但是也带来了更复杂的读写。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122046553.png" alt="image-20221112204647199" style="zoom:67%;" />

### Review: Magnetic Disks

- 磁盘的读写时间：在这个设备的排队时间＋控制器时间＋寻道时间+旋转时间+传输时间

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122048112.png" alt="image-20221112204812761" style="zoom:67%;" />

### Typical Numbers for Magnetic Disk

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122049387.png" alt="image-20221112204904051" style="zoom:67%;" />

### Disk Performance Example

- 从例子可以看出，对同一磁道上的块进行连续的读取，速度是最快的。**所以在设计我们的文件系统的时候，要利用好这一点，尽量保证好局部性**，否则文件系统的性能会很差。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122049559.png" alt="image-20221112204930217" style="zoom:67%;" />

### Lots of Intelligence in the Controller

- 控制器的一些智能功能，包括
  - 扇区备用：透明地将坏的扇区映射到同一面上的好的备用扇区
  - [Slip sparing](https://en.wikipedia.org/wiki/Sector_slipping)：扇区滑动。重新映射所有扇区（当存在坏扇区时）以保留连续访问的特性。
  - 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122054408.png" alt="image-20221112205420075" style="zoom: 67%;" />

## Solid State Disks (SSDs)

- 没有旋转和寻道
- 使用电子来表示比特零和一。有电子为1，无电子为0。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122145472.png" alt="image-20221112214513097" style="zoom:67%;" />

### SSD Architecture – Reads

- 不管是随机读取还是顺序读取，都能达到最高带宽。
- 延迟时间不存在寻道和旋转时间。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122145576.png" alt="image-20221112214557233" style="zoom:67%;" />

### SSD Architecture – Writes

- 写的时候，需要先擦除，然后再重写，不能直接覆盖，因为电子没有办法直接覆盖。
- 写的耗时是读的十倍，擦除的耗时是写的十倍。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122146360.png" alt="image-20221112214650023" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122148305.png" alt="image-20221112214759973" style="zoom:67%;" />

### Solution – Two Systems Principles

- SSD在实虚拟块和物理的块之间做了一个映射，类似虚拟内存和物理内存之间的映射，由SSD的控制器负责管理，FTL。
- OS与虚拟块进行交互，而控制器与物理块交互。
- 对逻辑上的同一页写入的时候不去覆写物理页，而是在空闲页上直接写入新的版本，然后在FTL修改虚拟页到物理页的映射关系。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122148148.png" alt="image-20221112214826821" style="zoom:67%;" />

### Flash Translation Layer

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122151328.png" alt="image-20221112215152014" style="zoom:67%;" />

### SSD Summary

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122152005.png" alt="image-20221112215240669" style="zoom:67%;" />

## Ways of Measuring Performance: Times (s) and Rates (op/s)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122206988.png" alt="image-20221112220606636" style="zoom:67%;" />

## What Determines Peak BW for I/O?

- 主要是硬件
- 还有排队。这就涉及到我们的排队理论。在吞吐量很大的时候，排队延迟会成为不可忽略的一部分。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122206813.png" alt="image-20221112220645461" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211122209209.png" alt="image-20221112220950854" style="zoom:67%;" />

# CS162 Lecture 19: Filesystems 1: Performance (Con't), Queueing Theory, Filesystem Design

## Little’s Law (B$\Rightarrow$λ)

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131513204.png" alt="image-20221113151358894" style="zoom:67%;" />



## A Simple Systems Performance Model

- 延迟 = 排队延迟d + 操作所需时间t。或者说，排队时间+服务时间
- 利用率应该不大于1。
- $\mu_{max}$是由整个系统的瓶颈部分决定的上限值。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131513461.png" alt="image-20221113151310103" style="zoom:67%;" />

## Ideal System Performance

- 理想情况下：蓝线，在请求率小于最大服务率时，服务率随请求率正比例增长，当请求率达到最大服务率后，服务率不再增长，保持水平不变。
- 实际情况：绿线。以一种曲线的形式贴近蓝线。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131516077.png" alt="image-20221113151609771" style="zoom:67%;" />

## Some Results from Queuing Theory

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131800047.png" alt="image-20221113180002710" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131800881.png" alt="image-20221113180015557" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131800723.png" alt="image-20221113180032425" style="zoom:67%;" />

由排队理论，我们可以得到实际情况（有随机突发的I/O请求）下的性能情况（延迟情况）。

- 可以看到随着利用率的增大，延迟陡增，趋于正无穷。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131801941.png" alt="image-20221113180110612" style="zoom:67%;" />

## A Little Queuing Theory: An Example

- 可以使用排队理论的结论来计算。

- $\lambda$：10次每秒
- $\mu$：一次服务需要20毫秒，一秒50次($\frac{1}{T_S}$)。
- $\rho = \frac{\lambda}{\mu} = 0.5$
- 平均的服务时间$T_S$:20ms
- 平均的排队时间：$T_Q = \frac{1+C}{2} * \frac{\rho}{1-\rho} * T_S$。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131802267.png" alt="image-20221113180219948" style="zoom:67%;" />

## Optimize I/O Performance

如何优化I/O性能？

- 让每一步都更快
- 使用更多并行的系统，比如更多独立的总线和控制器
- 优化瓶颈

队列可以吸收突发，平滑曲线流，但是存在延迟无穷大的风险。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131812419.png" alt="image-20221113181207069" style="zoom:67%;" />

## When is Disk Performance Highest?

- 大量的连续的顺序读取的时候
- 有很多请求，对这些请求重新排序，以达到连续读取。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211131817893.png" alt="image-20221113181705568" style="zoom:67%;" />

## Disk Scheduling (1/3)

磁盘调度

- FIFO
- 最短寻道时间优先，SSTF。
  - 可能会导致饥饿。
- 电梯算法。
- Circular-Scan

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151502497.png" alt="image-20221115150218123" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151508113.png" alt="image-20221115150830759" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151509607.png" alt="image-20221115150908258" style="zoom:67%;" />

# CS162 Lecture 20: Filesystems 2: Filesystem Design (Con't), Filesystem Case Studies

## Building a File System

什么是一个文件系统？

- OS的一层，它将硬盘的以块为单位的接口转换为文件、目录等等。
  - 使用有限的硬件接口（块的数组）来提供一个更加方便以及有用的接口。
- 文件系统有如下的接口：
  - 命名：使用文件名来找到文件而不是使用块号。
  - 组织文件名和目录
  - 维护文件和块的映射关系
  - 提供一定的保护以及可靠性。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151532108.png" alt="image-20221115153235766" style="zoom:67%;" />

## Recall: User vs. System View of a File

- 用户的角度来看，文件是有结构的数据。
- 而从系统调用的角度来看，文件只是一个由字节构成的集合，它并不知道如何解读这些字节，也不关心这些字节的结构，只有用户知道如何解读这些字节。
  - 用户想要存储怎样的数据结构对于系统调用来说是毫无意义的。
- 从OS内部来看，文件就是一个由块构成的集合（块是逻辑上传输的最小单元，扇区是物理上最小的传输单元，通常会将多个扇区组成一个块）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151539091.png" alt="image-20221115153928770" style="zoom:67%;" />

## Translation from User to System View

- 用户有一个由字节构成的文件，文件与文件系统交流，文件系统与硬盘交流。
- 用户说给我2到12字节时会发生什么？
  1. 如果这些字节没有在缓存中，文件系统从硬盘请求与文件的2到12字节对应的一块（4KB），然后找到第2到12字节，打包发给用户。
  2. 否则直接从缓存中将2到12字节打包发给用户
- 如果想写入2到12字节？
  1. 文件系统找到与这个文件的2到12字节对应的块，**将其放入内存，然后在内存中修改2到12字节，然后再写回硬盘**。
  2. **不能直接进入硬盘，然后找到硬盘上对应的块，直接在硬盘上修改第2到12字节**
- 文件系统内的一切都是以完整大小的块为单位。
  - 实际的硬盘I/O以块为单位发生。
  - 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151542032.png" alt="image-20221115154247693" style="zoom:67%;" />

## Disk Management

- 硬盘上的两个基本实体：

  - 文件：在逻辑空间中按顺序排列的用户可见的块组。
  - 目录：用户可见的从文件名到文件的索引映射。

- 硬盘作为线性扇区数组访问。

- 如何识别一个扇区？

  - 在硬盘还没有现在那么大的时候，使用物理位置。现如今硬盘变得越来越大，BIOS跟不上这么大的硬盘，所以不再使用了。
    - 每个扇区的位置其实是一个由$[柱面,盘面,扇区]$构成的向量来描述的。
  - 现在使用逻辑块地址（LBA）：
    - 每个扇区由一个整数来标识的地址，从1到硬盘容量。
    - 控制器负责将逻辑块地址翻译为物理位置。
    - LBA对OS屏蔽了硬盘的物理结构。

  

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151605845.png" alt="image-20221115160512512" style="zoom:67%;" />

## What Does the File System Need?

文件系统需要什么？

- 追踪空闲块。这样才知道该把新写入的数据放哪里。
- 追踪哪块包含哪个文件的数据（维持块和文件的映射关系）
- 在目录中管理文件

我们将这些信息也保存在硬盘上，这些信息本身也是文件。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151612395.png" alt="image-20221115161234067" style="zoom: 67%;" />

## Data Structures on Disk



- 在硬盘中的数据结构与在内存中不一样，在内存中可以使用指针。思想是一样的，但是在硬盘中只能以块为单位构建数据结构。
- 一次只能访问一个块，而不能访问某个单独的字。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151620987.png" alt="image-20221115162031667" style="zoom:67%;" />

## File System Design

### Critical Factors in File System Design

在设计文件系统的时候，我们需要考虑到的一些关键的因素：

- 优化硬盘性能。最大化连续访问，最小化寻道时间。对于SSD来说，由于其随机读写的速度与连续读写基本无异，所以有些针对HDD的优化是不必要的。
- 在读写之间先打开：
  - 我们可以在打开文件的过程中提前做保护性的检查，检查权限之类的，以及找到实际的文件位置在哪里、
- 仔细地分配和回收空闲块。
- 在目录中管理文件

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151638473.png" alt="image-20221115163811125" style="zoom:67%;" />

### Components of a File System

- inumber：IndexNumber，可以看做是一个指向inode的指针，或者说是inode数组的索引。
- inode：保存各种文件元数据，以及记录硬盘上的哪些块是对应这个文件的结构。当你新建一个文件的时候，实际上就新建了一个inode。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151647238.png" alt="image-20221115164703884" style="zoom:67%;" />

#### Recall: Abstract Representation of a Process

在最开始时，我们认为，一个进程打开文件时，OS会维护一个打开文件描述对象，这个对象里面记录了打开的文件是哪一个文件。

那时我们认为打开文件描述对象使用文件名来记录，像下图的`foo.txt`。但是实际上它使用的是相应文件的inumber，而不是文件的名字。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151656618.png" alt="image-20221115165602282" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151658121.png" alt="image-20221115165826790" style="zoom: 50%;" />





- 已知文件名和偏移量，通过目录来得到inumber（file number），然后借助inumber查找index structure得到inode，最后得到文件的块。
- 读和写都是在file number上运行的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151706507.png" alt="image-20221115170612171" style="zoom:67%;" />

### How to get the File Number(inumber)?

如何获取一个文件的inumber（file number）？

- 一个目录本身就是一个文件，它保存了`<文件名,inumber>`的映射关系。
  - `inumber`可以是一个文件的file number，也可以是另外一个目录的file number
  - OS将这个映射关系存储为它自己可以解读的格式。
  - 每个`<文件名,inumber>`的映射关系叫做一个目录项(Directory entry)
- 所以要获取一个文件的inumber，从目录结构去读取即可。
- 进程是不允许读取一个目录的原始字节流的。
  - 系统调用`read()`只能用于读取文件，而不能用于读取目录。
  - 需要读取目录的话使用`readdir()`，它遍历整个映射关系，同时不会暴露出原始的字节流。
  - 因为这样不安全，会有进程破坏目录文件的风险。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151921043.png" alt="image-20221115192132682" style="zoom:67%;" />

### Directory Abstraction

- 目录是一个专用的文件。

  - 其内容：`<文件名,file number>`映射对的列表。

- 访问目录的系统调用：

- libc库的支持：

  - `DIR`结构体是一个描述目录的结构体

  - `dirent`结构体：Directory entry结构体。

  - ```c
        DIR * d = opendir("E:\\Unix");
        while (1) {//进程已结束,退出代码-1073741819 (0xC0000005)
            struct dirent* dirent = readdir(d);
            printf("%s\n",dirent->d_name);
        }
    ```

    

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151928849.png" alt="image-20221115192832501" style="zoom:67%;" />

## Directory Structure

- 如何解析路径`/my/book/count`?`header`：就是指`inode`。
  1. 根目录root，也即`/`，的`inode`通常在硬盘上的固定位置，找到根目录的`root_inode`。
  2. 查找`root_inode`，找到与`my`对应的`inumber`，找到`my`的`my_inode`
  3. 查找`my_inode`，找到与`book`目录对应的`inumber`，找到`book`的`book_inode`。
  4. 查找`book_inode`，找到与`count`文件对应的`inumber`，找到其`inode`。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211151955897.png" alt="image-20221115195506570" style="zoom:67%;" />

## In-Memory File System Structures

从下图可以看出，在内核空间中，每个进程有一个`open-file`表，这个表中的文件描述符指向系统范围上的`open-file`表的某一项。

- Open系统调用：根据路径名从硬盘中找到对应的`inode`，然后将`inode`读取到内存，存储在系统范围上的`open-file`表中。
  - 对于同一个文件多个打开的实例，表中都只有一项。
  - 一旦我们建立了in memory inode，我们就不再关心文件名有多长，访问文件的速度就与文件名的长度无关
- Read和Write系统调用就借助处于内存中的`inode`来实现其功能。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152007937.png" alt="image-20221115200735575" style="zoom:67%;" />

## Characteristics of Files

- 文件所具有的的特点：
  - 大部分的文件都很小，小体积的文件在数量上占大多数。
  - 大部分的字节处于大文件中，虽然小体积的文件数量多，但是数量较少的大体积文件反倒占据了大部分的字节（大体积的文件在总体积上占大多数）。

## Case Study:FAT: File Allocation Table

- 学习文件系统的第一个实例：FAT。
- FAT：文件分配表。一般来说这个表在硬盘上会有多个副本，增强可靠性。
- 目前我们假定我们可以通过目录来由文件名得到文件的file number。
- FAT表可以看做是一个整数的数组（当然，每一项不仅仅是整数），数组的第$i$项（项的整数的值也是i）与硬盘中的第$i$个块(block)相对应，是一个一一映射。
- 假定我们要访问文件31的第2块，文件31在硬盘中各个块的分布如图。
  1. 找到其file number，也就是31。
  2. 然后找到FAT的第31项，这项与文件31的Block 0相对应，这一项同时有一个指针，指向这个文件的下一个块所对应的FAT表项。
  3. 沿着指针找到Block 1，然后沿着Block 1的指针找到Block 2。
  4. 将Block 2读取到内存。
  5. 如果我们想要访问file 32？实际上不存在file32，因为这会把我们带到FAT表的第32项，而这一项是file 1的Block 1。并不是每一个file number都与一个文件的开头对应。
- FAT有许多版本，FAT12、FAT32。后面的数字指的是整数的位数，FAT32的整数就是32位的，FAT表项则最多有$2^{32}$项。
- 未被使用的块在FAT中对应的表项被标记为FREE。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152103232.png" alt="image-20221115210347902" style="zoom:67%;" />
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152136134.png" alt="image-20221115213634795" style="zoom:67%;" />

- FAT存储在硬盘上，某个特定的位置（开始？）
- 如何格式化一个硬盘？
  - 将所有的块都写零，同时将所有的FAT项标记为FREE。
- 如何快速格式化一个硬盘？
  - 将FAT表全部标记为FREE，但是不需要去删除硬盘上的内容。
- 可以看到，在右侧的图上有了两个文件FILE #2和FILE #1。我们由图可以得出如下结论：
  - 指针并不永远是朝下或者朝上的，FILE#2的第1个（从零计数）Block对应的FAT项就在第0个对应的FAT项的上面。
  - 这也是FAT的一个很大的问题，它不具有局部性，同一个文件的多个块可能散落分布在FAT的世界各地。
  - FILE #1也就是file 31，相比之前多增加了一块，block 3，这就是新写入的内容。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152140987.png" alt="image-20221115214010632" style="zoom:67%;" />

### FAT: Directories

- 一个目录就是一个包含文件名到文件号的映射的文件。
- 在FAT中，目录是一个链表，如下图：
  - Name：文件名，其中`.`和`..`是当前目录和父目录，Music和Work应该也是目录？（看没有文件后缀猜的）。
  - File Number：文件号。
  - Next：指向下一个节点的指针。
  - 需要线性查找整个表来找到特定的项。
- **在FAT中，文件的属性（权限什么什么的，元数据什么什么的）是保存在目录中的**。
- 从哪里找到根目录(`'/'`)？
  - 一个在磁盘上明确定义的位置。
  - 对于FAT来说，这个位置是Block 2（没有Block 0 和1）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152151152.png" alt="image-20221115215114817" style="zoom:67%;" />

### ![](https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152159224.png)

### FAT Discussion

- 找到块的时间？线性的，因为每个文件的所有FAT表项实际上就构成了一个链表，我们必须从头开始沿着指针查找。
- 文件的块的分布？如果不做什么处理的话，基本上就是散落在各个地方。
- 连续访问？沿着指针走就行了
- 随机访问？性能很差，因为没办法直接到达随机的位置，必须从头开始沿着指针走。
- 大小文件的处理都还行。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152159224.png" alt="image-20221115215953886" style="zoom:67%;" />

## Case Study:Unix File System (Berkeley FFS)

FFS：Fast File System

- 关于Berkeley BSD 4.1 FFS的缺点的一些总结：
  - 我们可以从inode数组的结构以及inode的结构看出，它们的结构并不能使得块在硬盘上以一种相对聪明的方式排布（也就是，没有保证局部性，没有保证同一个文件的所有块是相邻的）
  - 实际上最初的Berkeley Unix 4.1 bds的文件系统有一个非常不幸的特点：最开始的时候文件系统的速度会非常快，为什么？因为我们可以保证我们写入的新文件的块在磁盘上都是连续的，所以将它们读取回来会很快。但是随着时间的推移，读读写写删删改改，文件系统会变得越来越慢，直到其速度越来越低，因为磁盘上的块会随机地散布在硬盘上。因为在最初的bsd上的Free List是一个链表，它不知道局部性。
  - 所以这个文件系统是需要优化的，以使得读取的时候能做到：先读取同一个轨道上的，然后读取同一个柱面上的（因为同一个柱面上不需要移动读头），也就是连续读取。
  - BSD 4.2的FFS优化了这个问题。下节课的内容

### Inodes in Unix (Including Berkeley FFS)

- File Number是inode数组的索引，所以File Number就是inumber。
  - inode的数组，就是由inode对象构成的数组：`inode[] inodes = new inode[xxx] `
- 索引结构就是这个inode数组。
  - inumber是数组的索引，也是目录保存的<file_name,file number>映射的第二个分量。
  - 每个inode与一个文件对应，并且包含了它的元数据。
    - 目录也是文件，所以一个目录也与一个inode对应。根目录的inode处于固定位置。
    - 找到根目录的inode，读取根目录文件，就可以得到其他子目录的<目录名，inumber>映射了。
    - 所以，像读写权限之类的是存储在inode内的（with file），而不是在目录内。
    - 允许同一个文件具有不同的名字，只需要在目录中添加`<Name1，inumber1>,<Name2，inumber1>`，以此类推，的映射关系（Directory entry）就行了。只要inumber相同，这两个不同名字的文件实际上就是指向硬盘上的同一个文件。

- Inode数组维护了一棵多级的树状结构来找到文件的块。
  - 对大文件和小文件都很好，为了做到这一点，这棵树是非对称的（待会阐释非对称的）。
  - 文件的块是固定大小的，比如4KB的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152254839.png" alt="image-20221115225424446" style="zoom:67%;" />

### Inode Structure

- 下面是一个Inode数组，以及单个的Inode的结构。
  - 这个数组中每个元素都是一个Inode，只不过有的Inode处于使用中，有的inode是未使用的。
  - 使用中的inode与一个文件是一一对应的关系。
- 我们根据Inumber找到数组中的第Inumber项，然后这一项就是一个Inode结构体（或者说一个Inode对象）
- 一个Inode对象由如下结构构成：
  - 文件的元数据：
    - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152306287.png" alt="image-20221115230611697" style="zoom: 33%;" />
  - 直接指针：直接指向硬盘中文件的数据块。
  - 间接指针：包括一重间接指针，二重间接指针，三重间接指针。
    - 一重间接指针指向间接指针块，在这个块中是一系列的直接指针。
    - 二重间接指针指向二重间接指针块，在这个二重间接指针块中是一系列的一重间接指针。
    - 三重间接指针指向三重间接指针块，这个三重指针块又指向很多个二重指针块，每个二重指针块又指向很多个一重指针块，每个一重指针块又指向很多个数据块。
  - 所谓的非对称，就是指的是由直接指针和间接指针构成的树，这棵树是不对称的，直接指针那边树的深度明显更小。
  - 小文件只需要占用直接指针就足够了，而大文件则可以利用间接指针来指向更多的数据块。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152304861.png" alt="image-20221115230447510" style="zoom:67%;" />

- 一个来自下一节的更好看的图：
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171543260.png" alt="image-20221117154315839" style="zoom:67%;" />

### Small Files: 12 Pointers Direct to Data Blocks

- 假如Inode有12个直接指针，硬盘每个块的大小是4KB，那么对于文件大小在48KB以下的小文件来说，使用直接指针来记录文件位置就够了。同时直接指针访问起数据块来也更快。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152313407.png" alt="image-20221115231331034" style="zoom:67%;" />

### Large Files: 1-, 2-, 3-level indirect pointers

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211152329036.png" alt="image-20221115232923629" style="zoom:67%;" />

## Fast File System (BSD 4.2, 1984)

- BSD 4.2 的FFS的简单介绍，下节课的内容。
- Inode的结构和BSD4.1是一样的。块的尺寸从1024变为4096字节，即1K到4K。
- 关于FFS的一篇论文：
- 相对于BSD4.1 FFS的优化：
  - 分布式的inodes，而不是单独一个全局的inode数组，这样使得inode离数据本身更近（这样寻道时间就更短）
  - 使用bitmap替代FreeList
  - 试图连续分配文件
  - 保留百分之十的硬盘空间

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171502123.png" alt="image-20221117150217751" style="zoom:67%;" />

### FFS Changes in Inode Placement: Motivation

FFS改变Inode放置位置的几个动机：

- 早期的Unix（的FFS）和DOS/Windows的FAT文件系统上，header数组（FAT表，inode array）存储在硬盘的最外侧的柱面上。
  - 固定大小的数组，所以inode的数量是固定的，在硬盘被格式化时建立。
  - 每个给定一个唯一的数，Inumber。
- 第一个问题：所有的inode处于同一个地方，鸡蛋放在同一个篮子里是不安全的。
  - 一旦我们失去了inode数组，我们就失去了所有的文件。
  - inode数组离它指向的数据不近。
    - 读取一个小文件，先要寻道找到对应的inode，然后再寻道回去读取数据。
- 第二个问题：当创建一个文件的时候，不知道它将来会变得多大。
  - 那么你应该给一个文件分配多少连续的空间是比较合适的？
  - 使得优化性能很困难。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171506736.png" alt="image-20221117150651380" style="zoom:67%;" />

# CS162 Lecture 21: Filesystems 3: Case Studies (Con't), Buffering, Reliability, and Transactions

## Case Study:Berkeley Fast File System (FFS)

FFS的子代还在Linux的Ext2和3，BSD版本的UFS文件系统。

### FFS Locality: Block Groups

- 将每一个盘面上的轨道分为多个组，每个组是离得很近的轨道的集合。
- 每个块组有属于它的inode数组和FreeSpaceBitmap，这样一个inode和与它关联的文件的数据块就可以处于同一个组。并且，假如有一个含有文件的目录，目录的inode和这些文件的inode也会与对应的数据块处于同一个组。
  - 这样，在一个组内移动磁头的性能就没有那么差。
- 文件的元数据、数据块本身、空闲空间都在同一个块组，避免了在用户数据和系统结构之间寻道的巨大开销。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171604989.png" alt="image-20221117160442611" style="zoom:67%;" />

- 对于小文件，或者刚刚被创建出来的文件，FFS填充本地的块组的空闲空间。当文件写入的大小超过一个阈值时，FFS去往另外一个块组，从中找到一大块的连续的空闲块（bitmap上连续的一串零就是一大串的连续的空闲块），然后再继续写入。这样当读取大文件时，我们只会偶尔地寻道（连续读取一大块——寻道——再连续读取一大块——寻道），而不是读取几个块就来来回回的移动读头，因此我们可以获得相对高的性能。
- **保持百分之十的空闲空间是使得这玩意工作的重要条件**。
  - 保持百分之十的空闲空间使得找到一大块连续空闲块的可能性足够高。
- FFS好处总结：
  - 对于小文件和小目录，能够将所有的数据块、inode等等放在同一个柱面上，这样就没有寻道。
  - 可靠性：由于将inode和对应的文件放在一个块组，即使读头触碰到了某个轨道甚至块组，他也只会破坏掉在这个轨道上（块组）的inode数组和文件。但是其他块组上的inode数组和数据块都是完好无损的。所以这是一个很大的可靠性有点。因为鸡蛋不在一个篮子里（如果我们把一个块组看做一个篮子的话）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171611938.png" alt="image-20221117161145577" style="zoom:67%;" />

### UNIX 4.2 BSD FFS First Fit Block Allocation

最后再提一嘴块分配的事。

Bitmap的每一个比特与一个块按顺序对应（第一个比特对应第一个块，第二个比特对应第二个块..），一一映射。比特为1表示占用，0表示空闲。这样，只要发现Bitmap上有一大串连续的0，就可以判断这是一大块空闲的空间。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171632976.png" alt="image-20221117163212626" style="zoom:67%;" />

### Attack of the Rotational Delay

旋转延迟问题，一个很有意思的问题。经常出现在老的系统上。

- 这个问题的叙述是这样的：随着磁盘的旋转，读头在读取某条轨道上它需要的数据，在FFS开始的时候的某些老系统上，读头读取了一个块到内存之后，它要做一些别的工作，等它返回来要读取下一个块的时候，这个块恰好就越过了读头，于是读头就需要等一整圈的旋转来读取下一个块。
- 解决方法：Skip Sectoring。使得读头返回的时候，下一个需要的块的开头恰好就在读头下面。FFS实现了这个解决方法。
- 但是对于如今的硬盘来说，你直接就把整个轨道读取到硬盘的缓存里就行了。这样无所谓旋转延迟的，因为缓存是RAM做的，后续的读取直接读取缓存就行了。
  - 缓存可以是硬盘控制器自带的缓存
  - 也可以是OS来负责这个工作。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171650529.png" alt="image-20221117165025178" style="zoom:67%;" />

### UNIX 4.2 BSD FFS

- 好处：
  - 对于大文件和小文件的存储都处理的很好。（Inode的12个直接指针+多个间接指针+多重间接指针的结构决定的）
  - 对于大文件和小文件的局部性都处理的很好。（Inode和数据块放在一起，Block Group）
  - 元数据和数据的局部性。
- 坏处：
  - 对于非常非常小的文件来说不高效。（即使是只有一个字节的文件，它也有一整个inode(inode都比数据大了)，和至少一个数据块，意思是有3.999KB的内部碎片。这一点是由Inode的结构决定的，至少需要一个直接指针（也就是一个数据块）来存放数据块）
  - 

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171653651.png" alt="image-20221117165355319" style="zoom:67%;" />

## Hard Links

什么是硬链接

- 一个硬链接就是一个目录项，Directory Entry，一个`<file_name,inummber>`的映射。
- 当一个文件被创建出来的时候，它的第一个硬链接也同时被建立起来。
- 使用`link()`系统调用来创建额外的硬链接，使用`unlink()`移除硬链接。
  - 如果使用`unlink()`移除了唯一的一个硬链接，那么对应的文件就会被删除，因为所有的资源都会在这时被回收。
- 文件什么时候能被删除？
  - 当再也没有指向这个文件的硬链接的时候，这个文件会被删除。
  - 处于这个目的，Inode中维护了硬链接的引用计数。
  - 当你打开一个文件的时候，你也有了一个对这个文件的硬链接。当然，是处于内存中的。所以当你打开一个文件，然后在没有关闭它的时候试图删除它，是删不掉的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211171928355.png" alt="image-20221117192504850" style="zoom:67%;" />

## Soft Links (Symbolic Links)

什么是软链接

- 软链接或者符号链接，有些OS叫它快捷方式。
- 软连接是包含路径名和文件名的映射的目录项，将一个名字映射到另外一个名字。
- 硬链接：`<file name,inumber>`，软链接：`<file name, dest filename>`
- cmd:`ln -s`创建软链接

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172004044.png" alt="image-20221117200444689" style="zoom:67%;" />

## Directory Traversal

当访问`/home/cs162/stuff.txt`时发生了什么？

1. `/`根目录的Inumber是预先定义在内核中的，比如说2。
2. 根据`/`的Inumber，我们找到`/`的inode，找到`inode`后，我们利用`inode`里的直接指针和间接指针，读取根目录`/`的目录文件。
3. 遍历目录文件，找到`<home,home_inuber>`映射，得到目录home的目录文件的Inumber，找到对应的inode。
4. 读取home目录的inode，找到home目录的目录文件，读取，遍历，找到`<cs162,inumber>`，找到目录cs162的Inumber，找到对应inode。
5. 读取cs162目录的inode，找到cs162目录的目录文件，遍历，找到`<stuff.txt,inumber>`，找到`stuff.txt`的Inumber，找到对应的inode。
6. 读取`stuff.txt`的inode，找到其文件本体的数据块，读取（当然，要建立对应的文件描述对象）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172013045.png" alt="image-20221117201336684" style="zoom:67%;" />

- 问题：如果inode数组满了怎么办？inode用完了就没办法创建新的文件了啊，因为一个inode对应一个文件，而inode数组又是固定大小的，所以用完了inode就没办法创建更多的文件了。**所以存在一种可能：硬盘空间没用完，但是不能创建更多的文件了，因为inode用完了（而文件都很小，小到总大小都不能塞满硬盘）**

## Large Directories: B-Trees (dirhash)

对于很大很大的目录，可以建立B树索引。可选的，Linux没有。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172025370.png" alt="image-20221117202500016" style="zoom:67%;" />

## Case Study:Windows NTFS

- 留着吧，没太看懂。

## Memory Mapped files

- 传统的I/O包括显式的从进程的地址空间的缓存来自/去往文件的传输。
  - 这个过程涉及将多个副本复制到内存中的缓存中，同时包括open，read，write，close等系统调用。
- 如果我们能将文件直接映射到我们的地址空间中的某个可用区域呢？这样我们就可以读写内存区域，隐式地将文件page in和page out，文件读写就看上去和内存读写一样。
- 当我们使用`exec`来启动进程时，可执行文件就是以这种方式处理的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172112110.png" alt="image-20221117211209748" style="zoom:67%;" />

### Using Paging to mmap() Files

- 使用`mmap`系统调用将文件映射到虚拟地址空间的某个区域，也即创建一些指针指向这个文件。
- 然后在读取这个地址的时候，触发`PageFault`，由对应的异常处理程序将文件的一部分读取进DRAM。
- 然后进程再重试读取这个地址。
- Used both for manipulating files and for sharing between processes

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172120048.png" alt="image-20221117212036676" style="zoom: 50%;" />

## The buffer cache

- 用户看来，一切都是字节为单位的，而在硬盘上，都是以块为单位传输，所以内核必须要解决掉这个不匹配。这也是buffer cache的起始。
  - 内核要从硬盘中以块为基本单位读取数据并放入内存。
  - 著名的进程要读取4个字节，OS从硬盘拖出了一整块4KB。

- 内核必须要将硬盘的块复制到内存以读取它们的内容以及将它们写回，如果编辑过。
  - 可能是数据块，可能是inode，可能是目录的内容。（这些都可以被修改，所以也可以被写回内存，当然，也可以被读取）
  - 可能是脏的
- 主要思想：通过在内存中缓存硬盘数据以利用局部性。
- Buffer Cache：被用于缓存内核资源的内存，包含硬盘的块和名字翻译。
  - 可以包含脏块。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172141018.png" alt="image-20221117214106666" style="zoom:67%;" />

### File System Buffer Cache

举个例子来说明文件系统的Buffer Cache可以用来干嘛：

- Blocks是RAM中的硬盘缓存本体，State是与之关联的一个状态表，每个Block格子对应State的一个格子。
- Blocks中缓存了：
  1. Inode，可能是文件的Inode，也可能是目录的Inode
  2. Directory Data Block
  3. Free Bitmap，这个是硬盘上用于记录磁盘可用块的比特图。
- 当我们打开一个文件时，我们就会把对应的Inode缓存在Buffer Cache中，然后利用这个inode做到快速的对文件读写。
- 也可以对缓存中的数据进行写，State会将对应的块标记为脏。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172209317.png" alt="image-20221117220922943" style="zoom:67%;" />

### Buffer Cache Discussion

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172226047.png" alt="image-20221117222635690" style="zoom:67%;" />

### File System Caching

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172227904.png" alt="image-20221117222702545" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172228882.png" alt="image-20221117222822530" style="zoom:67%;" />

### File System Prefetching

- 预取：利用大多数常见文件访问是顺序访问的事实，在当前读取请求之前预取后续磁盘块

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172231974.png" alt="image-20221117223158618" style="zoom:67%;" />

### Delayed Writes

- Write对文件的写实际上是先写在缓冲区里的，它不一定会立刻就被写回磁盘。
  - write将数据从用户空间复制到内核的buffer cache，所以它可以快速地返回用户空间。
- read可以看到write写入的结果，因为它也看缓存。所以即使数据还没被写回硬盘，也能被它看到。
- 来自write系统调用的数据什么时候被写入硬盘？
  - 当缓存满的时候。
  - 当缓存被周期性冲刷的时候。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172236656.png" alt="image-20221117223648295" style="zoom:67%;" />

#### Delayed Writes (Advantages)

第四点：有些文件在创建出来之后，在缓冲区刷新之前就又被删除了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172247209.png" alt="image-20221117224758843" style="zoom:67%;" />

#### Buffer Caching vs. Demand Paging

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211172251361.png" alt="image-20221117225116011" style="zoom:67%;" />

# CS162 Lecture 22: Transactions (Con't), End-to-End Arguments, Distributed Decision Making

## Recall: COW with Smaller-Radix Blocks

- 在更新完毕之前，保留旧的数据块。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191309873.png" alt="image-20221119130937533" style="zoom:67%;" />

## Transactions

- 事务，用于保证对于文件的更新是原子性的，要么完全更新要么不更新。
  - 将原子性的概念从RAM扩展到持久化的存储上。
- 为了保证事务，有许多种临时的方法
  - Unix的FFS。
  - COW with Smaller-Radix Blocks。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191307639.png" alt="image-20221119130654265" style="zoom:67%;" />

### Typical Structure

- 一个事务的典型结构：
  - 开始一个事务。
  - 做一系列的更新，如果出现任何的错误，回滚。如果与其他事务出现冲突，回滚。
  - 提交这个事务。提交后这个事务所做的一切更改都将被永久保留下来。在我们提交事务之前，我们所做的任何更新都不会永久影响文件的内容。

### Transactional File Systems

- 对于文件系统的改变都被当做事务来对待。
- 当一个事务被写入log的时候，它被提交。
  - 数据是被写入硬盘的，处于可靠性的需求。
  - 也可能为了加速而写入非易失性的RAM中。
- 尽管文件系统可能不会被立即更新，但是数据被保存在了log中。
- “日志结构的”和“日志的”的区别：
  - 在一个日志结构的文件系统中，数据保留在log中
  - 在一个日志的文件系统中，Log用于恢复。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191323287.png" alt="image-20221119132329939" style="zoom:67%;" />

### Journaling File Systems

- 不直接修改硬盘上的数据的结构。
- 将每个更改以事务的形式记录在log中
- 一旦更改被记录在了log中，这些更改就可以安全地应用于文件系统。
- 垃圾回收：一旦一个更改被应用了，那么就可以从log中删去它的项。
- Linux给原始的类FFS文件系统（Ext2）加了个日志得到了EXT3。
  - 可选项：是否将数据写入日志，还是只写入元数据。并不会对性能造成重大影响，因为日志是连续的。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191328762.png" alt="image-20221119132814402" style="zoom:67%;" />

### Creating a File (No Journaling Yet)

- 在不使用log的情况下创建一个文件：
  1. 找到空闲的数据块
  2. 找到空闲的inode项
  3. 找到目录项插入点
  4. 将块标记为使用中
  5. 写入inode，让其指向数据块
  6. 在目录项中插入一个映射关系。
- 如果我们在创建文件的图中崩溃了，比如没有写入映射关系，那么我们就会有一个指向数据块的inode不处于任意一个目录中，这个inode就丢了。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191350802.png" alt="image-20221119135015463" style="zoom:67%;" />

### Creating a File (With Journaling)

- 在有日志的情况下创建一个文件：
  - 找到数据块巴拉巴拉巴拉
  - 写入日志：
    1. 写入日志：对Bitmap的更改
    2. 写入日志：对inode的更改
    3. 写入日志：对目录项的更改

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191404595.png" alt="image-20221119140439244" style="zoom:67%;" />



- 在写入日志提交之后，重新执行整个事务。
- 所有的对于文件系统的访问都会首先查看日志。
  - 因为硬盘上的文件可能是过时的。
- 当将更改复制到磁盘之后，就会从log中删去其对应的事务。
- 如果在提交日志之后系统崩溃了，可以根据日志来做恢复。如果在恢复的过程中又崩溃了，根据日志从头开始整个恢复过程。

### Crash Recovery: Discard Partial Transactions

- 在恢复的过程中，扫描日志。
- 如果发现有事务只有开始而没有提交，那么将这个事务整个抛弃，磁盘则保持在未更改的状态。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191414934.png" alt="image-20221119141404580" style="zoom:67%;" />

### Journaling Summary

- 现代的文件系统可以选择只在日志中记录元数据。
  - 只记录对文件系统数据结构的更改，但是直接更新文件的内容。
  - 最坏的情况下，当系统崩溃，并且还没来得及将数据刷新，那么你就得到了一个全是垃圾的文件。Tradeof betweem reliability and performance。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191416766.png" alt="image-20221119141642387" style="zoom:67%;" />

## The Log Structured File System (LFS)

- Log本身就是文件系统。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191449415.png" alt="image-20221119144934036" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191449122.png" alt="image-20221119144948772" style="zoom:67%;" />

## What about Flash Filesystems?

- SSD之类的与磁盘有一个很大的不同，就是没有办法直接覆写同一块。先擦除，然后再写入。
- 我们可以针对这一点来构建一个文件系统。FTL会使得这个过程容易一些。
- <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191511408.png" alt="image-20221119151147024" style="zoom:67%;" />

### Example Use of LFS: F2FS: A Flash File System

- 没有细讲。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191513547.png" alt="image-20221119151323200" style="zoom:67%;" />

## Centralized vs Distributed Systems

从这里开始，进入了分布式系统的间接。

- 中心化系统：C-S架构，单个的物理机服务器负责完成主要的功能。
- 分布式系统：P2P架构，所有的物理机是对等的实体

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191524946.png" alt="image-20221119152453570" style="zoom:67%;" />

### Distributed Systems: Motivation/Issues/Promise

我们为什么会整出分布式系统这玩意？

分布式系统的前景：

- 更高的可用性：一台机器倒了，用另外一台
- 更好的耐操性：可以将数据保存在多个机器上，多个冗余备份，异地容灾巴拉巴拉。
- 更安全：...

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191534401.png" alt="image-20221119153411018" style="zoom:67%;" />

### Distributed Systems: Reality

然而事实不是我们想的那样。

协作相对中心化系统更加困难，存在数据一致性问题，而这个问题在中心化的系统上是很容易解决的。

另外还有一个信任的问题，能否信任其他的机器？相信其会履行协议，而不会反过来攻击系统？

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191542329.png" alt="image-20221119154212951" style="zoom:67%;" />

### Distributed Systems: Goals/Requirements

- 总结起来就是透明性。将整个系统的复杂度掩盖在一个简单的接口的后面。
- 可能的透明性：
  - 地点：不需要知道资源的物理位置
  - 迁移：在用户没有感知的情况下将资源迁移，而不影响用户的使用。
  - 副本：用户不需要知道底层究竟存在多少个副本，在用户看来就是只有一个文件
  - 并发性：多个用户可以同时访问，而所有用户都不需要知道还有其他多少用户
  - 并行性：系统可能透明地将任务分解为多个小的任务同时进行以加速，而不需要告知用户
  - 容错性：隐藏出错的部分，而不影响用户的正常使用
- 所谓透明性的目的，并不是说不让用户知道，而是说如果用户不关心，那他就没必要知道，但是如果用户关心，我们可以适度打破透明性以满足要求。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191559809.png" alt="image-20221119155956444" style="zoom:67%;" />

- 分布式系统的实体之间使用协议来进行通信。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191620339.png" alt="image-20221119162024951" style="zoom: 50%;" />

## End-To-End Argument

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191633505.png" alt="image-20221119163357133" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191634011.png" alt="image-20221119163415633" style="zoom:67%;" />

### End-to-End Principle

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191635886.png" alt="image-20221119163527529" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191636931.png" alt="image-20221119163617585" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191646419.png" alt="image-20221119164653051" style="zoom:67%;" />

# CS162 Lecture 23: Distributed Decision Making (Con't), Networking and TCP/IP

## Recall: Distributed Applications

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/image-20221119190419683.png" style="zoom:67%;" />

## Distributed Consensus Making

分布式共识

- 共识问题：
  - 所有的节点都有一个值
  - 有些节点可能会崩溃并且停止响应
  - 最后，所有的剩余节点要从所有节点提出的值的集合中选定同一个值（建立共识）。
- 分布式决策：
  - 在true与FALSE中选择
  - 或者在提交和终止之间选择
- 同样重要的一点是，需要让做出的决定不被遗忘。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191917269.png" alt="image-20221119191730741" style="zoom:67%;" />

## Two-Phase Commit

- 分布式事务：两台或多台机器在做某件事或者不做某件事上达成一致，**原子性地**。
  - 没有时间上的限制，不要求同时。
  - 假如有20台机器，那么这20台机器要么全部决定做这件事，要么全部决定不做这件事，不会存在一部分决定做，另外一部分决定不做的情况。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191938140.png" alt="image-20221119193800582" style="zoom:67%;" />

### Two-Phase Commit Protocol

两阶段提交协议

- 一旦系统中的一个节点做出了决定，我们必须确保这个决定不会被回滚，所以我们需要在每台机器上都有持久性的稳定log。
- log用于跟踪一个提交是否发生了。
  - 如果一台机器崩溃了，那么它重启的第一件事就是检查log以恢复到崩溃前的状态。
- 准备阶段：
  - 全局的协调者要求所有参与者承诺提交或回滚事务。
  - 参与者们将其承诺写入log，然后向协调者确认。
  - 如果有任何一个参与者投票决定终止，协调者将Abort写入它的log，然后告诉所有的参与者Abort，每个参与者在其log中记录Abort。
  - 只要有一个节点不同意提交，那么就不可能提交。
- 提交阶段：
  - 在所有的参与者都回应它们准备好了，协调者就将Commit写入其log。
  - 然后要求所有的节点提交，他们用ACK回应。
  - 在收到ACK后，协调者将Got Commit写入log。
- Log用于保证要么所有的机器提交了要么所有的机器没有。



<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211191949750.png" alt="image-20221119194943203" style="zoom:67%;" />

### 2PC Algorithm

两阶段提交算法：

- 一个全局协调者
- N个工作者（复制品）
- 高层次地对算法描述：
  - 协调者问所有的工作者能不能提交。
  - 如果每个工作者都回复可以提交，那么协调者广播“全局提交”的消息，否则协调者广播“全局放弃”的消息。
  - 工作者遵循全局的消息。
- 如果协调者询问的某一台机器由于下线了而没有回复，那么协调者会在超时之后决定发送全局终止的消息（为了保证原子性）。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201315811.png" alt="image-20221120131520405" style="zoom:67%;" />

#### Two-Phase Commit: Setup

基本上就是照着PPT念了

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201338105.png" alt="image-20221120133855743" style="zoom:67%;" />

#### Two-Phase Commit: Preparing

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201339050.png" alt="image-20221120133923684" style="zoom:67%;" />

#### Two-Phase Commit: Finishing

- **Because no machine can take back its decision, exactly one of these will happen**

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201339457.png" alt="image-20221120133943113" style="zoom:67%;" />

#### Detailed Algorithm

- 如果Woker发送了VOTE-BORT，那么它可以不用等待协调者就立即Abort，因为它知道接下来会发生什么（GLOBAL-ABORT）

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201346417.png" alt="image-20221120134642031" style="zoom:67%;" />

- 成功的例子：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201348656.png" alt="image-20221120134820303" style="zoom:67%;" />

#### State Machine of Coordinator

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201348357.png" alt="image-20221120134842004" style="zoom:67%;" />

#### State Machine of Workers

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201349013.png" alt="image-20221120134902659" style="zoom:67%;" />

#### Dealing with Worker Failures

处理Worker出错：也许是丢包了，也许是机器崩了

协调者在发送了VOTE-REQUEST之后，会进入计时等待状态，如果在超时之前没有收到全部的N个Worker的Vote，那么协调者将超时信号当做Abort。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201349262.png" alt="image-20221120134946885" style="zoom:67%;" />

#### Dealing with Coordinator Failure

处理协调者失败：

- 如果Worker是在INIT状态下，等待来自协调者的VOTE-REQUEST超时，那么Worker将超时看做是Abort。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201357046.png" alt="image-20221120135755687" style="zoom:50%;" />
- 如果Woker是在READY状态下，等待来自协调者的GLOBAL-*消息，那么Worker**不能独自做出决定**，只能一直阻塞，等待协调者恢复并发送GLOBAL消息。
  - <img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201358427.png" alt="image-20221120135808036" style="zoom:50%;" />
- 问题：Worker是怎么知道协调者收到了自己的消息的？Well，它们不知道。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201353154.png" alt="image-20221120135304786" style="zoom:67%;" />

#### Durability

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201400959.png" alt="image-20221120140035606" style="zoom:67%;" />

#### Blocking for Coordinator to Recover

一个正处于等待全局决定的Worker可以向其他的Worker询问它们的状态：

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201404593.png" alt="image-20221120140431209" style="zoom:67%;" />

### Distributed Decision Making Discussion (1/2)

- 为什么想要分布式决策？
  - 容错率高
- 为什么2PC没办法解决General's paradox？
  - 因为2PC不要求同时做出同样的决定（进攻的时刻在同一时刻），只要求最终所有的节点做出同样的决定。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201405427.png" alt="image-20221120140529959" style="zoom:67%;" />

- 2PC一个很麻烦的地方：如果有一台Worker不断地崩溃，那么会导致所有的Worker都无法Commit，只能一直Abort

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201409288.png" alt="image-20221120140903934" style="zoom:67%;" />

### Alternatives to 2PC

2PC的替代品：

- 3PC：三阶段提交，多一个阶段，允许节点出错而继续有所进展。
- PAXOS
- RAFT：因为PAXOS太复杂而提出的

如果有一个或多个节点是恶意的？

- 恶意：指节点试图破坏分布式决策。
- 解决方法：Byzantine Agreement and Block Chains

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201413370.png" alt="image-20221120141354992" style="zoom:67%;" />

## Byzantine General’s Problem

[拜占庭将军问题](https://blog.csdn.net/weixin_44885334/article/details/126487783)

- N个玩家，一个主将，N-1个副将。
  - 其中的某些玩家可能是有恶意的（不忠诚的），记作f，甚至将军本身都可能是有恶意的（假如他发出矛盾的命令）。
  - 不忠诚的玩家可以做出任何事情（比如欺骗其他的副将，比如阳奉阴违）。
- 主将给他的N-1个副将发送命令，并且要符合以下约束：
  1. 所有忠诚的副将采取一样的行动（遵循一样的命令，如果将军是不忠诚的，那么副将采取的命令不一定和将军的命令一致）
  2. 如果将军是忠诚的，那么所有的副将遵循将军发出的命令。
- 即便将军是不忠诚的，我们依旧要保证所有忠诚的副手能够做出同样的行为，即便这个行为不是将军所命令的那样。
- 这个问题最有意思的地方在于，即便一个副将知道哪一条消息是谁告诉它的，它也不能判断那个人是否是忠诚的。因为不忠诚的一方可以表现的很忠诚。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201432547.png" alt="image-20221120143241175" style="zoom:67%;" />

## Byzantine General’s Problem (con’t)

- 拜占庭将军问题在n>3f的情况下才能解决，否则是无法解决的。

- 举例：三个玩家，一个将军两个副手
  - 其中一个副手是不忠诚的。将军发出一个命令给两个副手，不忠诚的副手欺骗忠诚的副手说将军发出了相反的命令。此时忠诚的副手没有办法决定应该采取哪一种行动。
  - 将军不忠诚：将军给两个副手分别发出相反的命令即可。两个副手相互交流的时候就会发现彼此收到的命令矛盾。
- 有许多的算法来解决这个问题，原始的算法是指数级的复杂度，更新的算法是$O(N^2)$。使用Block Chain可以优化到线性。
- 拜占庭将军问题的算法可以用于解决分布式系统中的分布式决策问题。
  - 当系统中不超过三分之一的节点是恶意的时候，依然可以得出一个分布式的决策（并且是忠诚的机器达成一致的）。
- 这个算法的最大的限制在于，必须保证系统中的恶意节点不超过总数的三分之一。假如节点不断遭到破坏，那么修复节点的速度就必须高于破坏的速度，否则一旦恶意节点超出总数的三分之一，整个算法就不再适用。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201447692.png" alt="image-20221120144747310" style="zoom:67%;" />

## Is a BlockChain a Distributed Decision Making Algorithm?

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201511885.png" alt="image-20221120151159505" style="zoom:67%;" />

- 是

# CS162 Lecture 24: Networking and TCP/IP (Con't), RPC, Distributed File Systems

## Question: Data Representation

任意一个对象都有一个特定于机器的二进制的表现形式。同一个对象在不同机器上的二进制形式可能是不一样的（比如大端小端）

- 互联网是大端的。

- 在没有共享内存的情况下，外部化一个对象需要我们把它变成一个连续的字节序列，
  - 序列化/编组：将对象表示为字节序列
  - 反序列化/解组：在目的地从其编组形式重建原始对象

### Simple Data Types

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201702879.png" alt="image-20221120170244479" style="zoom:67%;" />

### Dealing with Endianness

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201703972.png" alt="image-20221120170349610" style="zoom: 67%;" />

### What About Richer Objects?

使用各种标准化的序列化格式，JSON/XML

## Remote Procedure Call (RPC)

远程程序调用

- 基本思想：让通信看起来像一个常规的函数调用。
- 客户端视角来看，它只是调用了一个函数，然后得到了其返回值而已。但是实际上这个调用是发生在远程的服务器上的，服务器完成操作后将结果发回给客户端。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201707679.png" alt="image-20221120170755221" style="zoom:67%;" />

## Microkernel operating systems

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201721506.png" alt="image-20221120172109103" style="zoom:67%;" />

## the CAP Theorem

CAP理论：

是指一个分布式的计算机系统不可能同时满足以下三个条件，通常只能达成其中两条：

C - Consistency，即一致性，指对于外部观察者而言，无论访问多少次，访问哪一个节点，系统提供的答复（某份数据）总是最新的、一致的；换言之，只要系统的状态发生改变，所有节点都必须获知该变化并从此返回状态改变后的数据。
A - Availability，即可用性，指每一次外部的访问，都能得到包含数据的答复，但系统不保证数据是最新的。
P - Partition-tolerance，即分区容错，指在网络出现问题，部分节点之间无法正常通信时，系统依旧可以提供服务而不受影响。

<img src="https://raw.githubusercontent.com/CorneliaStreet1/NewPicBed0/master/202211201721808.png" alt="image-20221120172138419" style="zoom:67%;" />

# CS162 Lecture 25: Distributed Storage, NFS and AFS, Key Value Stores

## Distributed File Systems

