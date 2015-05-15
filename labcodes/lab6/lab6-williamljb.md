# lab6 报告

## 练习题
### 练习一 使用 Round Robin 调度算法
---

问题回答：init为初始化调度队列；enqueue为把一个进程放入队列中；dequeue为把一个进程弹出队列；
pick_next为从队列中拿出一个合适的进程作为下次运行的进程。

初始化之后，系统通过schedule来调度进程。schedule中首先把当前进程放入队列，然后挑选一个合适的进程运行，
然后让它出队并运行。round robin算法维护一个队列，当进程时间片用完时，调用schedule，把它取出来放到队尾，
再取出当前队头作为下一个执行的进程。

在class里面维护n个队列。在proc_struct中新增pos记录上次进程位于哪个队列中，enqueue的时候push进第pos个队列中，
tick的时候让re_sche等于1，调用schedule，dequeue的时候把它从队列中取出，并把pos加一。

---

### 练习二 实现 Stride Scheduling 调度算法
---

实现过程：按照注释，在初始化的时候把队头设置为null，并把进程计数器置零。enqueue时用斜堆的merge合并，
更新它的time_slice和进程计数器。tick的时候和RR算法相同。dequeue时用斜堆的删除，并把计数器减一。
pick的时候，选出队头，并把它的stride加上BIG_STRIDE / priority即可。

需要注意的是，内核线程的priority均为0，如果没有判断，会出现异常。

和答案不一样的是，在之前的lab中，当tick到了TICK_NUM的时候会清零，这会导致priority程序死循环，因为它的每个线程
都要到time为1000的时候才退出，而gettime返回的直接是tick，所以这里需要改一下。另外，现在已经不需要TICK_NUM了，
每次tick加一的时候都需要查看进程执行情况，如果还是每TICK_NUM查看一次的话，priority不会输出正确。

还有就是，这样修改了之后，由于时间片太短，每5ms就切断一次，导致forktree程序输出结果会有嵌套，使得grade程序误认为forktree
输出错误；而如果调高MAX_TIME_SLICE的话，priority程序也不能输出12345的结果，通常会有12344。经过测试之后，
发现lab6_result也有类似的现象。

---

---


### 重要的知识点
---

实验中stride算法的处理在原理课中并没有详细说明。
原理课中讲到多级反馈队列调度算法在实验中也没有得到实现。

---

