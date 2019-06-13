# [Operating Systems: Three Easy Pieces](http://pages.cs.wisc.edu/~remzi/OSTEP/#book-chapters)

### To Students

> Ideas are not pulled out of the air.

《Intel 64 and IA-32 Architectures Software Developers Manual》 以下简称 *IA32*

> 这本书每章的 summary 的 `ASIDE` 基本就是这一章的内容了

- [I. Virtualization](#i)
  - [4. The Abstraction: The Process](#4)
  - [7-10. Scheduling](#7-10)
  - []()
  - []()
- [II. Concurrency](#ii)
  - [30. Condition Variables](#30)
- [III. Persistence](#iii)


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

