# lab4 报告

## 练习题
### 练习一 分配并初始化一个进程控制块
---

实现过程：在alloc_proc函数中把proc_struct中的所有成员变量都进行一次初始化即可。
具体地，state为PROC_UNINIT，pid为-1，cr3为boot_cr3，runs、kstack、need_resched、parent、mm、context、tf、flags、name均为0。
其中，context和name需要用memset来初始化。

和答案不一样的是，之前没有找到context的合适的初始化函数，以及以为name是没有分配过的空指针于是用了kmalloc。
其实他们都只需要用memset就可以完成初始化。
另外，这里的kstack可以不设置为boot_kstack，我觉得原因为之后的分配工作中会再次设置kstack的值所以没有关系。

---

### 练习二 为新创建的内核线程分配资源
---

实现过程：按照注释中的每一句话来设置即可。需要注意的是在添加链表指针之前需要设置好pid，否则会出现unhandled page fault。

和答案不一样的是，我在alloc_proc中直接把parent设置为current，我认为在当前版本也不会有什么不对的，不过也许之后会不兼容。
然后就是不清楚hashlist用的是pid，所以在ret的时候才去分配pid，或者是忘记让nr_process加一了，导致出现unhandled page fault。
最后是答案中的local_intr_save/restore，把这段删去也没有什么影响，但是这段应该是不能被中断打断的，所以这两句话也是必须的。

---

### 练习三 阅读代码，理解 proc_run 函数和它调用的函数如何完成进程切换的。
---

首先调用load_esp0和lcr3加载新的esp和cr3，完成栈和页表基址的切换。然后用汇编函数switch_to完成上下文的保存和加载，
之后用ret即可返回到切换目标的上次中断处继续执行。

创建并运行了两个内核线程，分别为idleproc和init_main。

local_intr_save/restore的作用为保存当前的中断使能，并禁止/打开中断。如果当前已经禁止中断，则不需要做任何事情。
在这里的作用就是防止这段程序被中断打断。

---

---


### 重要的知识点
---

实验中对hashlist的添加所需要的前提条件没有在原理课上讲到，虽然这个只是一个细节。
原理课中讲到关于线程的各种状态转移都是从实验的代码复制过来的，所以并没有什么是实验中没有的。

---

