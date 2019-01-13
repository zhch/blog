# xv6-modules

## 1 kalloc

| 函数      | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| kinit1    | 把kernel后的位置(end)到物理地址4M，全部分隔成4K一个Page串到freelist上 |
| kinit2    | ？                                                           |
| freerange | 把一个物理地址的范围串联上freelist（freelist上保存的都是线性地址） |
| kfree     | 把给定的线性地址所指向的Page放回freelist                     |
| kalloc    | 分配一页，返回页的线性地址                                   |



## 2 vm

| PRIVATE/PUBLIC | 函数                                                         | 作用                                                         |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| PUBLIC         | void seginit(void)                                           | 初始化当前CPU段描述符表，进入每一个CPU前会执行一次           |
| PUBLIC         | freevm(pde_t *pgdir)                                         | 把给定页目录管理的所有页都释放                               |
| PUBLIC         | pde_t* setupkvm(void)                                        | 分配一个页目录，并在上面完成四个区域内存页的映射：1：IO Space；2：内核代码和只读数据；3：内核数据；4：设备memory map空间； 所有进程页表都会包含这几部分，但是没有权限访问。具体映射关系如图所示：https://user-images.githubusercontent.com/1244560/49500113-dff05b00-f8aa-11e8-8464-70fe59645905.png |
| PUBLIC         | switchkvm(void)                                              | 把分配好的内核页表载入CR3寄存器                              |
| PUBLIC         | kvmalloc(void)                                               | = setupkvm + switchkvm                                       |
| PUBLIC         | inituvm(pde_t *pgdir, char *init, uint sz)                   | 把init给出的内容（代码）载入到给定页表的虚拟地址0处          |
| PUBLIC         | int allocuvm(pde_t *pgdir, uint oldsz, uint newsz)           | 把给定进程页表所占的内存空间（HEAP空间）从oldsz扩展到newsz   |
| PUBLIC         | deallocuvm(pde_t *pgdir, uint oldsz, uint newsz)             | pgdir给定一个进程或者是内核的虚拟地址空间，按顺序释放其页表中的页，直到占用内存从oldsz降低到newsz |
| PUBLIC         | switchuvm(struct proc *p)                                    | 把任务描述寄存器切换到给定的进程。把页表也切换到给定进程的用户态页表。 |
| PUBLIC         | int loaduvm(pde_t *pgdir, char *addr, struct inode *ip, uint offset, uint sz) | ?                                                            |
| PUBLIC         | clearpteu(pde_t *pgdir, char *uva)                           | ?                                                            |
| PUBLIC         | pde_t*copyuvm(pde_t *pgdir, uint sz)                         | ?                                                            |
| PUBLIC         | char* uva2ka(pde_t *pgdir, char *uva)                        | ?                                                            |
| PUBLIC         | int copyout(pde_t *pgdir, uint va, void *p, uint len)        | ?                                                            |
| --             |                                                              |                                                              |
| PRIVATE        | static pte_t *walkpgdir(pde_t *pgdir, const void *va, int alloc) | 在给定的page dir中查找给定的va所在的page entry，若不存在且alloc ！= 0，则分配所需的page entry和page table |
| PRIVATE        | static int mappages(pde_t *pgdir, void *va, uint size, uint pa, int perm) | 把从va开始，size长度的线性地址映射到从pa开始的物理地址，并把相关页表信息安装到给定的page dir中 |
|                |                                                              |                                                              |
|                |                                                              |                                                              |


## 3 mp

| CATEGORY         | FIELD/FUNCTION                                   | 作用                                                         |
| ---------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| FIELD            | struct cpu cpus[NCPU]                            | 所有CPU的描述                                                |
| FIELD            | int ncpu                                         | CPU总个数                                                    |
| FIELD            | uchar ioapicid                                   | IO APIC ID                                                   |
| PUBLIC FUNCTION  | void mpinit(void)                                | 查找MP configuration table并初始化多核状态：(1)为每个CPU初始化APIC ID; (2)初始化IO APIC ID(看起来只支持一个IO APIC) |
| PRIVATE FUNCTION | static struct mp*   mpsearch1(uint a, int len)   | 从给定的地址开始，搜索len字节长度的范围，搜索MP Floating Pointer Structure |
| PRIVATE FUNCTION | static struct mp*  mpsearch(void)                | 搜索以下三个地址范围，查找MP Floating Pointer Structure。（调用mpsearch1）1) in the first KB of the EBDA; 2) in the last KB of system base memory; 3) in the BIOS ROM between 0xE0000 and 0xFFFFF. |
| PRIVATE FUNCTION | static struct mpconf*  mpconfig(struct mp **pmp) | 搜索 MP configuration table                                  |
|                  |                                                  |                                                              |



## 4 proc

| CATEGORY         | TYPE/FIELD/FUNCTION                    | 作用                                                         |
| ---------------- | -------------------------------------- | ------------------------------------------------------------ |
| TYPE             | struct context                         | 保存了一组寄存器的值，这组寄存器的值定义了一个执行上下文。其布局与swtch.S中的pushl指令调用顺序一致。 |
| --               | --                                     | --                                                           |
| PUBLIC FUNCTION  | void pinit(void)                       | 初始化proc模块                                               |
| PUBLIC FUNCTION  | int cpuid()                            | 获取当前CPU id                                               |
| PUBLIC FUNCTION  | struct cpu* mycpu(void)                | 获取当前正在执行CPU的元信息                                  |
| PUBLIC FUNCTION  | struct proc* myproc(void)              | 获取当前进程描述符                                           |
| PUBLIC FUNCTION  | userinit(void)                         | 初始化init进程                                               |
| PUBLIC FUNCTION  | int fork(void)                         | Create a new process copying p as the parent. Sets up stack to return as if from system call. Caller must set state of returned proc to RUNNABLE. |
| PUBLIC FUNCTION  | void forkret(void)                     | A fork child's very first scheduling by scheduler() will swtch here.  "Return" to user space. |
| PUBLIC FUNCTION  | void exit(void)                        | Exit the current process.  Does not return. An exited process remains in the zombie state until its parent calls wait() to find out it exited. |
| PUBLIC FUNCTION  | int wait(void)                         | Wait for a child process to exit and return its pid. Return -1 if this process has no children. |
| PUBLIC FUNCTION  | int kill(int pid)                      | Kill the process with the given pid.                         |
| PUBLIC FUNCTION  | void procdump(void)                    | Print a process listing to console.  For debugging.          |
| PUBLIC FUNCTION  | int growproc(int n)                    | 把当前进程的内存空间扩大n字节（n小于0时缩小空间）。即对应的页表映射的空间进行扩充或收缩。Return 0 on success, -1 on failure. |
| PUBLIC FUNCTION  | sched(void)                            | 相关条件判断，通过后通过swtch，切换到当前CPU的scheduler函数的执行上下文继续执行 |
| PUBLIC FUNCTION  | scheduler(void)                        | 每个CPU完成初始化后都会调用一份自己的scheduler函数：遍历进程列表，遇到的第一个RUNNABLE状态进程，把进程描述符和用户态页表切换过去，并调用shced切换到进程的用户态执行上下文继续执行。等到进程调用yield，或者是时钟中断等各种情况，再次通过调用shced到本CPU的scheduler函数继续执行。 |
| PUBLIC FUNCTION  | void yield(void)                       | 放弃在当前CPU上继续执行：把自己的状态由RUNNING置位RUNNABLE，并调用sched |
| PUBLIC FUNCTION  | void wakeup(void *chan)                | Wake up all processes sleeping on chan： 把所有chan上处于SLEEPING状态的进程置为RUNNING(调用wakeup1) |
| PUBLIC FUNCTION  | sleep(void *chan, struct spinlock *lk) | 原子的把当前进程持有的spinlock释放，并在给定的等待队列中等待，然后重新调度(调用sched)。重新唤醒后重新获得锁 |
| --               | --                                     | --                                                           |
| PRIVATE FUNCTION | struct proc* allocproc(void)           | 分配一个进程描述符                                           |
| PRIVATE FUNCTION | static void  wakeup1(void *chan)       | Wake up all processes sleeping on chan The ptable lock must be held:把所有处于SLEEPING状态的进程置为RUNNING |
|                  |                                        |                                                              |



## 5 swtch 

| CATEGORY       | FIELD/FUNCTION                                        | 作用                                                         |
| -------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| PUBLIC FUCTION | void swtch(struct context **old, struct context *new) | 把当前执行上下文相关寄存器推到当前栈上，构成了一个struct context，并把该struct context地址保存到*old。最后把new中保存的寄存器值load到相应寄存器中。换句话说该函数的功能是：把当前执行上下文保存到old，并切换到new定义的执行上下文继续执行 |
|                |                                                       |                                                              |
|                |                                                       |                                                              |



## pipe

| 函数                                              | 作用                                            |
| ------------------------------------------------- | ----------------------------------------------- |
| int pipealloc(struct file **f0, struct file **f1) |                                                 |
| pipeclose(struct pipe *p, int writable)           |                                                 |
| int pipewrite(struct pipe *p, char *addr, int n)  | 把给定地址的n字节数据写入到pipe                 |
| int piperead(struct pipe *p, char *addr, int n)   | 把pipe中的数据读取到指定的缓冲区，最多读取n字节 |
|                                                   |                                                 |



















## 6 spinlock
最基础的同步机制

| CATEGORY        | FIELD/FUNCTION                            | 作用                              |
| --------------- | ----------------------------------------- | --------------------------------- |
| PUBLIC FUNCTION | initlock(struct spinlock *lk, char *name) | 初始化spinlock结构                |
| PUBLIC FUNCTION | acquire(struct spinlock *lk)              | 加锁spinlock                      |
| PUBLIC FUNCTION | release(struct spinlock *lk)              | 释放spinlock                      |
| PUBLIC FUNCTION | getcallerpcs(void *v, uint pcs[])         | ?                                 |
| PUBLIC FUNCTION | int  holding(struct spinlock *lock)       | 判断当前CPU是否持有给定的spinlock |
| PUBLIC FUNCTION | pushcli(void)和popcli(void)               | 可重入的关闭、打开中断            |





## 7 sleeplock
基于spinlock实现。

| CATEGORY        | FIELD/FUNCTION                                  | 作用                                                         |
| --------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| PUBLIC FUNCTION | initsleeplock(struct sleeplock *lk, char *name) | 初始化一个sleeplock                                          |
| PUBLIC FUNCTION | acquiresleep(struct sleeplock *lk)              | 获取一个sleeplock。和spinlock相比，sleeplock在等待锁时可以释放当前CPU，让别的线程可以在上面执行。加锁后中断保持打开，因此不能用在interrupt handler中 |
| PUBLIC FUNCTION | releasesleep(struct sleeplock *lk)              | 释放一个sleeplock                                            |
|                 |                                                 |                                                              |
|                 |                                                 |                                                              |





## trap

| CATEGORY        | FIELD/FUNCTION             | 作用                                                     |
| --------------- | -------------------------- | -------------------------------------------------------- |
| FIELD           | vectors                    | 由vectors.pl生成，几号中断就由vectors[i]中断处理程序处理 |
| PUBLIC FUNCTION | tvinit(void)               | 初始化中断处理向量                                       |
| PUBLIC FUNCTION | idtinit(void)              | 把中断处理向量载入idt寄存器                              |
| PUBLIC FUNCTION | trap(struct trapframe *tf) | Trap处理总入口                                           |
|                 |                            |                                                          |










~