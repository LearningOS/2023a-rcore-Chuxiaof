author：冯楚箫 2023/10/29
### 实验报告：
1. 在`TaskControlBlock`中新增 Fields：
`pub syscall_times:[u32; MAX_SYSCALL_NUM]`和`pub start_time: usize`。在 TASK_MANAGER 初始化函数中增加其相应初始化值，在`run_first_task()` 和 `run_next_task()` 中增加其相应变化值。

2. 增加3个新的 TASK_MANAGER 成员函数，功能如下：更新当前task的对各个`syscall_times`数组； 返回当前 task 的`syscall_times`数组；返回这个task第一次被调度的时间。
3. 在对 System Call 进行分发的函数 `syscall()` 内调用 `TASK_MANAGER` 成员函数，根据 `syscall_id` 对 `syscall_times` 数组进行更新。
4. 修改`sys_task_info`函数使 `TaskInfo` 内容完整。具体为调用 `TASK_MANAGER` 成员函数获得当前 task 的`syscall_times` 数组；返回这个 task 第一次被调度的时间，获取系统当前时间相减计算即可得到要求的 time。

-------------------
### 简答作业
#### Q1
#### Q2
##### 2.1
刚进入 `__restore` 时，`a0` 代表了内核栈的栈顶 （当然也是 `__restore` 的参数）。
使用 `__restore`情况二： `trap_handler` 处理完trap之后会从调用 `trap_handler` 的下一条指令开始执行，此时需要从栈上的 Trap 上下文恢复的`__restore`。
使用 `__restore`情况二：当批处理操作系统初始化完成，或者是某个应用程序运行结束或出错的时候，我们要调用 `run_next_app` 函数切换到下一个应用程序，此时可以复用`__restore`从刚构造的特殊的 Trap 上下文中，恢复下一个应用程序执行的部分寄存器。

##### 2.2
L43-L48：这几行汇编代码特殊处理了`sstatus`, `sepc` 和 `sscratch` 3个CSR寄存器。
`sstatus` 的 `SPP` 字段会被修改为 CPU 当前的特权级（U/S）。
`sepc` 会被修改为 Trap 处理完成后默认会执行的下一条指令的地址。
`scause/stval` 分别会被修改成这次 Trap 的原因以及相关的附加信息。

##### 2.3
L50-L56：跳过 `x2` : 因为要基于它来找到每个寄存器应该被保存到的正确的位置;跳过 `x4` : `x4(tp)`是线程指针（指向线程局部变量区域），一般不会被用到，所以也无需保存。

##### 2.4
L60：该指令之后，`sp` 指向User stack顶，`sscratch` 指向Kernel stack顶。

##### 2.5
`_restore`：中发生状态切换在`sret`指令，从 S 模式返回 U 模式。

##### 2.6
L13：该指令之后，`sp`指向Kernel stack顶，`sscratch` 指向User stack顶。

##### 2.7
从 U 态进入 S 态发生在`ecall`指令。





**荣誉准则**
警告

请把填写了《你的说明》的下述内容拷贝到的到实验报告中。 否则，你的提交将视作无效，本次实验的成绩将按“0”分计。

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：
	>无
    
2. 此外，我也参考了 **以下资料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：
    > rCore-Tutorial-Book-v3: https://rcore-os.cn/rCore-Tutorial-Book-v3/
    
3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。