# 谈谈 Futex / Pthread

> 不知道为什么现在我这里 pthread 比较新的版本无法调试，旧版本比如2.3.3又太trivial。另外 kernel futex 我也不知道怎么调试，难受。。。   

## Futex



### Reference

- [futex(2) - Linux manual page](http://man7.org/linux/man-pages/man2/futex.2.html)
- [（do_futex）futex.c - kernel/futex.c - Linux source code (v2.6.39.4) - Bootlin](https://elixir.bootlin.com/linux/v2.6.39.4/source/kernel/futex.c#L2586)
- [LWN index - futex](https://lwn.net/Kernel/Index/#Futex)
- [A futex overview and update - LWN](https://lwn.net/Articles/360699/)
- [Fuss, Futexes and Furwocks: Fast Userlevel Locking in Linux](https://www.kernel.org/doc/ols/2002/ols2002-pages-479-495.pdf)
- [Futexes Are Tricky](https://akkadia.org/drepper/futex.pdf)
- [In pursuit of faster futexes - LWN](https://lwn.net/Articles/685769/)
- [Basics of Futexes](https://eli.thegreenplace.net/2018/basics-of-futexes/)
- [Futex Scaling for Multi-core Systems - youtube](https://www.youtube.com/watch?v=-8c47dHuGIY)
- [The Path of the Private FUTEX - youtube](https://www.youtube.com/watch?v=IYAPmbJpnEs)
- [Pthread Condvars: Posix Compliance and the PI gap - youtube](https://www.youtube.com/watch?v=D1hGc8qJCcQ)
- [futex requeueing feature, futex-requeue-2.5.69-D3 - LWN](https://lwn.net/Articles/32746/)
- condvar stronger ordering guarantees
  - [Bug 13165 - pthread_cond_wait() can consume a signal that was sent before it started waiting](https://sourceware.org/bugzilla/show_bug.cgi?id=13165)
  - [0000609: It is not clear what threads are considered blocked with respect to a call to pthread_cond_signal() or pthread_cond_broadcast() - Austin Group Defect Tracker](https://www.austingroupbugs.net/view.php?id=609)
  - [2190(i). Condition variable specification - WG21](http://www.open-std.org/jtc1/sc22/wg21/docs/lwg-defects.html#2190)
  - [（战略性规避）New condvar implementation that provides stronger ordering guarantees.](https://sourceware.org/git/?p=glibc.git;a=commit;h=ed19993b5b0d05d62cc883571519a67dae481a14)
- [Requeue-PI: Making Glibc Condvars PI-Aware.pdf](https://static.lwn.net/images/conf/rtlws11/papers/proc/p10.pdf)
- [Priority inheritance in the kernel - LWN](https://lwn.net/Articles/178253/)
- [PI-futex: -V1](https://lwn.net/Articles/177111/)
- [Futex Requeue PI.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/locking/futex-requeue-pi.rst)
- [Lightweight PI-futexes.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/locking/pi-futex.rst)
- [A description of what robust futexes are.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/locking/robust-futexes.rst)
- [The robust futex ABI.txt](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/Documentation/locking/robust-futex-ABI.rst)
- [Robust futexes - a new approach - LWN](https://lwn.net/Articles/172149/)
- [lightweight robust futexes: -V1 - LWN](https://lwn.net/Articles/172134/)
- [lightweight robust futexes: -V4 - LWN](https://lwn.net/Articles/172768/)
- [FUTEX : new PRIVATE futexes - LWN](https://lwn.net/Articles/229668/)
- [linux futex浅析](https://yq.aliyun.com/articles/6043)
- [Linux Futex的设计与实现](https://blog.csdn.net/jianchaolv/article/details/7544316)
- [LKML: Ulrich Drepper: [PATCH 0/3] 64-bit futexes: Intro 关于是否需要提供64bit-futex 32位lock怎么分配](https://lkml.org/lkml/2008/5/30/458)
- [linus喷人 - The everything-is-a-file principle (Linus Torvalds)](https://yarchive.net/comp/linux/everything_is_file.html)
- [[RFC patch 0/5] futex: Allow lockless empty check of hashbucket plist in futex_wake()](https://lkml.org/lkml/2013/11/25/651)
- [futexes: Avoid taking the hb->lock if there's nothing to wake up](https://github.com/torvalds/linux/commit/b0c29f79ecea)
- [Leading items - LWN](https://lwn.net/Articles/704817/)
- [Effcient Userspace Optimistic Spinning Locks](https://linuxplumbersconf.org/event/4/contributions/286/attachments/225/398/LPC-2019-OptSpin-Locks.pdf)
- [Deterministic Futexes Revisited](https://www.cs.hs-rm.de/~zuepke/papers/ospert2018.pdf)


&nbsp;   
## Pthread


### Reference

- [glibc.git/history - nptl](https://sourceware.org/git/?p=glibc.git;a=history;f=nptl;hb=HEAD)
- [glibc.git/tree - nptl/](https://sourceware.org/git/?p=glibc.git;a=tree;f=nptl;hb=HEAD)
- [（futex-uaddr用法）sysdeps/nptl/lowlevellock.h](https://sourceware.org/git/?p=glibc.git;a=blob;f=sysdeps/nptl/lowlevellock.h;hb=HEAD)
- [pthread-types](https://sourceware.org/git/?p=glibc.git;a=tree;f=sysdeps/htl/bits/types;h=882c66afb35ae08bc84dfceaca486ae09c8e8e30;hb=HEAD)
- [Multi-Threaded Programming With POSIX Threads](http://www.cs.kent.edu/~ruttan/sysprog/lectures/multi-thread/multi-thread.html)
- [PThreads Primer - A Guide to Multithreaded Programming](https://www8.cs.umu.se/kurser/TDBC64/VT03/pthreads/pthread-primer.pdf)
- [Design of the New GNU Thread Library](https://www.akkadia.org/drepper/glibcthreads.html)
  - obsolete，原来设计 N:M 双层调度，原因是硬件限制 task_struct 上限（但其实 linux 2.4 之后的设计是 per-cpu-tss 而非 per-process）
  - Scheduler Activations，看起来有点复杂。。。
  - 《IA32 sdm》 - 3.4.2 Segment Selectors 中表示 15:3 选择 8192 个 GDT/LDT
- [The Native POSIX Thread Library for Linux.pdf](https://www.akkadia.org/drepper/nptl-design.pdf)
- [CloudABI's POSIX threads support: hierarchical futexes](https://nuxi.nl/blog/2016/06/22/cloudabi-futexes.html)
- [Operating Systems Thread Synchronization: Implementation.ppt](https://tropars.github.io/downloads/lectures/M1_OS/lecture_12--Thread_synchro_implementation.pdf)
- [How different is a futex from mutex - conceptually and also implementation wise?](https://www.quora.com/How-different-is-a-futex-from-mutex-conceptually-and-also-implementation-wise)
- [Thin Lock vs. Futex](https://bartoszmilewski.com/2008/09/01/thin-lock-vs-futex/)
- [Condition variable with futex](https://www.remlab.net/op/futex-condvar.shtml)