# 操作系统基础知识篇

## 什么是操作系统？

我通过以下四点介绍什么操作系统：

* **操作系统（英语：Operating System，简称OS）是管理计算机硬件与软件资源的程序，是计算机系统的内核与基石；**
* **操作系统本质上是运行在计算机上的软件程序 ；**
* **为用户提供一个与系统交互的操作界面 ；**
* **操作系统分内核与外壳（我们可以把外壳理解成围绕着内核的应用程序，而内核就是能操作硬件的程序）。**

![&#x64CD;&#x4F5C;&#x7CFB;&#x7EDF;&#x5206;&#x5185;&#x6838;&#x4E0E;&#x5916;&#x58F3;](https://images.xiaozhuanlan.com/photo/2019/a72a930ca61bc2b424bb900c94c063cf.png)

操作系统分内核与外壳

## 进程和线程的区别是什么?

这个问题我们在[《剖析面试最常见问题之 Java 并发基础知识》](https://xiaozhuanlan.com/topic/6149802735#section)这一章节已经提到过一次，阅读过这篇文章的朋友可以检测一下自己是否掌握。

下图是 Java 内存区域，通过下图我们从 JVM 的角度来说一下线程和进程之间的关系。如果你对 Java 内存区域 \(运行时数据区\) 这部分知识不太了解的话可以阅读一下这篇文章：[《可能是把 Java 内存区域讲的最清楚的一篇文章》](https://snailclimb.gitee.io/javaguide/#/./java/jvm/Java内存区域)

![img](https://images.xiaozhuanlan.com/photo/2019/ff96fed0e2a354bb16bbc84dcedf503a.png)

img

从上图可以看出：一个进程中可以有多个线程，多个线程共享进程的**堆**和**方法区 \(JDK1.8 之后的元空间\)**资源，但是每个线程有自己的**程序计数器**、**虚拟机栈** 和 **本地方法栈**。

**总结：** 线程是进程划分成的更小的运行单位,一个进程在其执行的过程中可以产生多个线程。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反

## 进程有哪几种状态?

* **创建状态\(new\)** ：进程正在被创建，尚未到就绪状态。
* **就绪状态\(ready\)** ：进程已处于准备运行状态，即进程获得了除了处理器之外的一切所需资源，一旦得到处理器资源\(处理器分配的时间片\)即可运行。
* **运行状态\(running\)** ：进程正在处理器上上运行\(单核CPU下任意时刻只有一个进程处于运行状态\)。
* **阻塞状态\(waiting\)** ：又称为等待状态，进程正在等待某一事件而暂停运行如等待某资源为可用或等待 IO 操作完成。即使处理器空闲，该进程也不能运行。
* **结束状态\(terminated\)** ：进程正在从系统中消失。可能是进程正常结束或其他原因中断退出运行。

![process-state](https://images.xiaozhuanlan.com/photo/2019/d38202593012b457debbcd74994c6292.png)

process-state

对比：[线程的生命周期和状态?](https://xiaozhuanlan.com/topic/6149802735#section-11)

## 进程间的通信的几种方式?

下面这部分总结参考了:[《进程间通信IPC \(InterProcess Communication\)》](https://www.jianshu.com/p/c1015f5ffa74) 这篇文章，推荐阅读，总结的非常不错。

1. **管道/匿名管道\(Pipes\)** ：用于具有亲缘关系的父子进程间或者兄弟进程之间的通信。
2. **有名管道\(Names Pipes\)** : 匿名管道由于没有名字，只能用于亲缘关系的进程间通信。为了克服这个缺点，提出了有名管道。有名管道严格遵循**先进先出\(first in first out\)**。有名管道以磁盘文件的方式存在，可以实现本机任意两个进程通信。
3. **信号\(Signal\)** ：信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生；
4. **消息队列\(Message Queuing\)** ：消息队列是消息的链表,具有特定的格式,存放在内存中并由消息队列标识符标识。管道和消息队列的通信数据都是先进先出的原则。与管道（无名管道：只存在于内存中的文件；命名管道：存在于实际的磁盘介质或者文件系统）不同的是消息队列存放在内核中，只有在内核重启\(即，操作系统重启\)或者显示地删除一个消息队列时，该消息队列才会被真正的删除。消息队列可以实现消息的随机查询,消息不一定要以先进先出的次序读取,也可以按消息的类型读取.比FIFO更有优势。**消息队列克服了信号承载信息量少，管道只能承载无格式字 节流以及缓冲区大小受限等缺。**
5. **信号量\(Semaphores\)** ：信号量是一个计数器，用于多进程对共享数据的访问，信号量的意图在于进程间同步。这种通信方式主要用于解决与同步相关的问题并避免竞争条件。
6. **共享内存\(Shared memory\)** ：使得多个进程可以访问同一块内存空间，不同进程可以及时看到对方进程中对共享内存中数据得更新。这种方式需要依靠某种同步操作，如互斥锁和信号量等。可以说这是最有用的进程间通信方式。
7. **套接字\(Sockets\)** : 此方法主要用于在客户端和服务器之间通过网络进行通信。套接字是支持TCP/IP的网络通信的基本操作单元，可以看做是不同主机之间的进程进行双向通信的端点，简单的说就是通信的两方的一种约定，用套接字中的相关函数来完成通信过程。

## 线程同步的方式有哪些?

线程同步是两个或多个共享关键资源的线程的并发执行。应该同步线程以避免关键的资源使用冲突。操作系统一般有下面三种线程同步的方式：

1. **互斥量\(Mutex\)**：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问。比如 Java 中的 synchronized 关键词和各种 Lock 都是这种机制。
2. **信号量\(Semphares\)** ：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量
3. **事件\(Event\)** :Wait/Notify：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操

## 操作系统中进程的调度策略有哪些?

为了确定首先执行哪个进程以及最后执行哪个进程以实现最大CPU利用率，计算机科学家已经定义了一些算法，它们是：

* **先到先服务\(FCFS\)调度算法** : 从就绪队列中选择一个最先进入该队列的进程为之分配资源，使它立即执行并一直执行到完成或发生某事件而被阻塞放弃占用CPU时再重新调度。
* **短作业优先\(SJF\)的调度算法** : 从就绪队列中选出一个估计运行时间最短的进程为之分配资源，使它立即执行并一直执行到完成或发生某事件而被阻塞放弃占用CPU时再重新调度。
* **时间片轮转调度算法** : 时间片轮转调度是一种最古老，最简单，最公平且使用最广的算法，又称RR\(Round robin\)调度。每个进程被分配一个时间段，称作它的时间片，即该进程允许运行的时间。
* **多级反馈队列调度算法** ：前面介绍的几种进程调度的算法都有一定的局限性。如**短进程优先的调度算法，仅照顾了短进程而忽略了长进程** 。多级反馈队列调度算法既能使高优先级的作业得到响应又能使短作业（进程）迅速完成。，因而它是目前**被公认的一种较好的进程调度算法**，UNIX操作系统采取的便是这种调度算法。
* **优先级调度** ： 为每个流程分配优先级，首先执行具有最高优先级的进程，依此类推。具有相同优先级的进程以FCFS方式执行。可以根据内存要求，时间要求或任何其他资源要求来确定优先级。

## 什么是虚拟内存\(Virtual Memory\)?

很多时候我们使用点了开了很多占内存的软件，这些软件占用的内存可能已经远远超出了我们电脑本身具有的物理内存。**为什么可以这样呢？** 正是因为 **虚拟内存** 的存在，通过 **虚拟内存** 可以让程序可以拥有超过系统物理内存大小的可用内存空间。另外，**虚拟内存为每个进程提供了一个一致的、私有的地址空间，它让每个进程产生了一种自己在独享主存的错觉（每个进程拥有一片连续完整的内存空间）**。这样会更加有效地管理内存并减少出错。

**虚拟内存**是计算机系统内存管理的一种技术，我们可以手动设置自己电脑的虚拟内存。不要单纯认为虚拟内存只是“使用硬盘空间来扩展内存“的技术。**虚拟内存的重要意义是它定义了一个连续的虚拟地址空间**，并且 **把内存扩展到硬盘空间**。推荐阅读：[《虚拟内存的那点事儿》](https://juejin.im/post/59f8691b51882534af254317)

维基百科中有几句话是这样介绍虚拟内存的。

> **虚拟内存** 使得应用程序认为它拥有连续的可用的内存（一个连续完整的地址空间），而实际上，它通常是被分隔成多个物理内存碎片，还有部分暂时存储在外部磁盘存储器上，在需要时进行数据交换。与没有使用虚拟内存技术的系统相比，使用这种技术的系统使得大型程序的编写变得更容易，对真正的物理内存（例如RAM）的使用也更有效率。目前，大多数操作系统都使用了虚拟内存，如Windows家族的“虚拟内存”；Linux的“交换空间”等。From:[https://zh.wikipedia.org/wiki/虚拟内存214](https://zh.wikipedia.org/wiki/虚拟内存214)

## CPU寻址?虚拟地址空间?为什么需要虚拟地址空间?

> 这部分内容参考了Microsoft官网的介绍，地址：[https://msdn.microsoft.com/zh-cn/library/windows/hardware/hh439648\(v=vs.85\).aspx26](https://msdn.microsoft.com/zh-cn/library/windows/hardware/hh439648%28v=vs.85%29.aspx26)

现代处理器使用的是一种称为 **虚拟寻址\(Virtual Addressing\)** 的寻址方式。**使用虚拟寻址，CPU需要将虚拟地址翻译成物理地址，这样才能访问到真实的物理内存。** 实际上完成虚拟地址转换为物理地址转换的硬件是 CPU 中含有一个被称为 **内存管理单元（Memory Management Unit, MMU）**的硬件。MMU 需要借助存放在内存中的页表来动态翻译虚拟地址，该页表由操作系统管理。

通过虚拟地址访问内存有以下优势：

* 程序可以使用一系列相邻的虚拟地址来访问物理内存中不相邻的大内存缓冲区。
* 程序可以使用一系列虚拟地址来访问大于可用物理内存的内存缓冲区。当物理内存的供应量变小时，内存管理器会将物理内存页（通常大小为 4 KB）保存到磁盘文件。数据或代码页会根据需要在物理内存与磁盘之间移动。
* 不同进程使用的虚拟地址彼此隔离。一个进程中的代码无法更改正在由另一进程或操作系统使用的物理内存。

![MMU\_principle\_updated](https://images.xiaozhuanlan.com/photo/2019/2b27dac8cc647f8aac989da2d1166db2.png)

MMU\_principle\_updated

最后两个问题都是关于操作系统内存管理的，这部分知识点理解起来还是挺简单的，更多内容比如：“页表”、“缺页”、”页面置换算法“等等推荐阅读下面几篇文章：

## 页面置换算法的作用?常见的页面置换算法有哪些?

> 这个题目经常作为笔试题出现，网上已经给出了很不错的回答，我这里只是总结整理了一下。

地址映射过程中，若在页面中发现所要访问的页面不在内存中，则产生缺页中断 。当发生缺页中断时，如果操作系统内存中没有空闲页面，则操作系统 必须在内存选择一个页面将其移出内存，以便为即将调入的页面让出空间。而用来选择淘汰哪一页的规则叫做页面置换算法，页面置换算法的作用是 **实现虚拟存储管理。**

* **OPT页面置换算法（最佳页面置换算法）** ：理想情况，不可能实现，一般作为衡量其他置换算法的方法。
* **FIFO页面置换算法（先进先出页面置换算法）** : 总是淘汰最先进入内存的页面，即选择在内存中驻留时间最久的页面进行淘汰。
* **LRU页面置换算法（最近未使用页面置换算法）** ：LRU（Least Currently Used）算法赋予每个页面一个访问字段，用来记录一个页面自上次被访问以来所经历的时间T，当须淘汰一个页面时，选择现有页面中其T值最大的，即最近最久未使用的页面予以淘汰。
* **LFU页面置换算法（最少使用页面排序算法）** : LFU（Least Frequently Used）算法会让系统维护一个按最近一次访问时间排序的页面链表，链表首节点是最近刚刚使用过的页面，链表尾节点是最久未使用的页面。访问内存时，找到相应页面，并把它移到链表之首。缺页时，置换链表尾节点的页面。也就是说内存内使用越频繁的页面，被保留的时间也相对越长。
