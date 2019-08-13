# [Operating Systems: Three Easy Pieces](http://pages.cs.wisc.edu/~remzi/OSTEP/#book-chapters)

### To Students

> Ideas are not pulled out of the air.

《Intel 64 and IA-32 Architectures Software Developers Manual》 以下简称 *IA32*

> 这本书每章的 summary 的 `ASIDE` 基本就是这一章的内容了   
> 感觉这本有点简单了，之前听别人说挺不错的。   
> 就当是查漏补缺了。

- [I. Virtualization](#i)
  - [4. The Abstraction: The Process](#4)
  - [7-10. Scheduling](#7-10)
  - [13. The Abstraction: Address Spaces](#13)
  - [15. Mechanism: Address Translation](#15)
  - [16. Segmentation](#16)
  - [18-20. Paging](#18-20)
  - [21-23. Virtual Memory System](#21-23)
- [II. Concurrency](#ii)
  - [30. Condition Variables](#30)
- [III. Persistence](#iii)
  - [36-38 I/O, HDD, RAIDs](#36-38)
  - [40-41 File System](#40-41)


&nbsp;   
<a id="i"></a>
## I. Virtualization

<a id="4"></a>
### 4. The Abstraction: The Process

- Virtualize CPU
  - low-level mechanisms to implement proecss
  - high-level policies to schedule

<img src="assets/6_2_limited_direct_execution.png" width="500"/>

&nbsp;   
<img src="assets/6_3_timer_interrupt.png" width="500"/>

#### 关于 contect swtich

参考 *IA32 §6.4.1*

<img src="assets/context_switch_1.png" width="500"/>

<img src="assets/context_switch_3.png" width="500"/>

<img src="assets/context_switch_2.png" width="500"/>

<img src="assets/context_switch_4.png" width="500"/>

<img src="assets/context_switch_5.png" width="500"/>

<img src="assets/context_switch_6.png" width="500"/>

<a id="7-10"></a>
### 7-10. Scheduling

trade-off：**turnaround time** 和 **response time**

- FIFO
- Shortest Job First (turnaround time)
- Preemptive Shortest Job First （有新来的可以换）
- Round Robin（都可以响应）
- Multi-level Feedback Queue
  - 混合方法
  - 进程被分配到队列（可能会被移动），队列内部使用某种调度算法
  - 对所有队列使用调度（可以按优先级分配时间），被选中的队列在内部执行调度
  - 需要比较合适的优先级改变算法，否则所有进程的对待方式都差不多
- Lottery Scheduling (Tickets)
- Completely Fair Scheduling（公平的虚拟运行时间）
- O(1) Scheduling
- BFS Scheduling

#### Multi-Core Scheduling

cache coherence, cache affinity

<a id="13"></a>
### 13. The Abstraction: Address Spaces

早期 OS 为了实现 time-sharing，给一个 process 整个地址空间，切换时将所有状态和数据保存到 disk。这样太慢，因此在切换时将 process 留在 memory，这样多个 proces 同时驻留在内存，于是面临一个问题：isolation 和 protection

OS 为每个 process 提供一段连续内存，仿佛 process 占有整个内存，把这种 abstraction 称作 address space

<a id="15"></a>
### 15. Mechanism: Address Translation

> In virtualizing memory, the hardware will interpose on each memory access, and translate each virtual address issued by the process to a physical address where the desired information is actually stored.

hardware 在每个 memory access 时做如下 address translation   
检查 virtual addr < limit register，然后计算 physical addr = base register + virtual addr   
OS 负责为每个 process 设置正确的 base register 和 limit register

<a id="16"></a>
### 16. Segmentation

分段为了解决内部碎片，因为分配固定大小的 limit register 导致浪费。

把 code, stack, heap 分开，每个作为单独的一段，有自己的 base register 和 limit register

<a id="18-20"></a>
### 18-20. Paging

将 memory 看作连续的 fixed-size slot，叫做 page frame

address translation：OS 把每个地址看成 （virtual page no + page offset），将 virtual page no 映射为 phsical page no

hardware 从 TLB 映射到 physical address，如果 TLB miss，交给 OS，OS 维护某种 page table 结构，利用特权指令修改 TLB，然后恢复到 TLB miss 之前，hardware 从 TLB 得到 physical address

- TLB O(1) 并行查找
- TLB 缓存的是一个 process 的映射，context switch 时，清空 TLB（或者提供区分进程的标记）

#### Page Table

OS 维护 TLB miss 的原因是 OS 可以设计高效的数据结构来维护 page table 映射。

多级页表（multi-level page table）：因为很多页表项是空的，所以做 indirect layer，指向存在的部分页表，省内存。


- [How The Kernel Manages Your Memory](https://manybutfinite.com/post/how-the-kernel-manages-your-memory/)
- [Anatomy of a Program in Memory](https://manybutfinite.com/post/anatomy-of-a-program-in-memory/)
- [Page table in Linux kernel space during boot](https://stackoverflow.com/questions/16688540/page-table-in-linux-kernel-space-during-boot)
- [Understanding the Linux Virtual Memory Manager](https://www.kernel.org/doc/gorman/html/understand/index.html)


<a id="21-23"></a>
### 21-23. Virtual Memory System

为了扩大地址空间，允许将内存 swap 到 disk，所以 page 增加一个属性 *present*，当发生 page fault 时，OS 负责维护：

- TLB 和 page table 同步
  - 即如果 TLB hit，那么 page 一定还在 memory
- page replacement policy
  - LRU
  - Clock
  - Victim

一个 page table entry 有3个属性：

- valid (segmentation fault)
- protect (protection fault)
- present (page fault)

<img src="./assets/ia32_4_level_paging.png" width="360"/>


&nbsp;   
<a id="ii"></a>
## II. Concurrency


<a id="30"></a>
### 30. Condition Variables

<img src="assets/30_1_signal_before_unlock.png" width="500"/>

这个其实很奇怪，我研究了一下，博客写在这里：[探究 “条件变量signal时是否需要持有mutex”](https://blog.csdn.net/rsy56640/article/details/84953204)

>尤其是参考最后《Programming with POSIX Threads》作者的论坛讨论

&nbsp;   

- Mesa semantics：被 wakeup 的线程有可能在 mutex 队列上阻塞
- Hoare semantics：被 wakeup 的线程立即拿到 mutex 并执行

>[Monitors and Condition Variables - Monitors Semantics](https://cseweb.ucsd.edu/classes/sp16/cse120-a/applications/ln/lecture9.html)

#### 由于大多数系统实现使用 Mesa semantics，所以总是使用 while-loop 来判断 cv 的 wait 条件

&nbsp;   
考虑生产者-消费者模型，要使用 **2** 个条件变量，因为生产者不应该唤醒另一个生产者，消费者也不应该唤醒另一个消费者。（若只有生产者会产生阻塞，可以仅使用 **1** 个条件变量）

&nbsp;   
signal 通常表示**资源可用**；而 broadcast 通常表示**状态改变**。由于有时线程 wait 的需求不相同（例如：请求内存大小），因此 signal 所唤醒的线程有可能仍然不满足 wait 条件，所以调用 broadcast，但是会产生惊群效应。（也可以手动在应用层管理调度）




&nbsp;   
<a id="iii"></a>
## III. Persistence


<a id="36-38"></a>
### 36-38 I/O, HDD, RAIDs

- OS interact with device
  - interrupt
  - DMA
- access device register
  - explicit I/O instructions
  - memory-mapped I/O

<img src="./assets/37_3_disk_track_and_head.png" width="400">

#### RAID

- capacity
- reliability
- performance

<a id="40-41"></a>
### 40-41 File System






<img src="./assets/.png" width="400">

<img src="./assets/.png" width="400">


<a id=""></a>
### 