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



## 4 spinlock

最基础的同步机制

| CATEGORY        | FIELD/FUNCTION                            | 作用                              |
| --------------- | ----------------------------------------- | --------------------------------- |
| PUBLIC FUNCTION | initlock(struct spinlock *lk, char *name) | 初始化spinlock结构                |
| PUBLIC FUNCTION | acquire(struct spinlock *lk)              | 加锁spinlock                      |
| PUBLIC FUNCTION | release(struct spinlock *lk)              | 释放spinlock                      |
| PUBLIC FUNCTION | getcallerpcs(void *v, uint pcs[])         | ?                                 |
| PUBLIC FUNCTION | int  holding(struct spinlock *lock)       | 判断当前CPU是否持有给定的spinlock |
| PUBLIC FUNCTION | pushcli(void)和popcli(void)               | 可重入的关闭、打开中断            |



## 5 sleeplock

基于spinlock实现。

| CATEGORY        | FIELD/FUNCTION                                  | 作用                                                         |
| --------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| PUBLIC FUNCTION | initsleeplock(struct sleeplock *lk, char *name) | 初始化一个sleeplock                                          |
| PUBLIC FUNCTION | acquiresleep(struct sleeplock *lk)              | 获取一个sleeplock。和spinlock相比，sleeplock在等待锁时可以释放当前CPU，让别的线程可以在上面执行。加锁后中断保持打开，因此不能用在interrupt handler中 |
| PUBLIC FUNCTION | releasesleep(struct sleeplock *lk)              | 释放一个sleeplock                                            |
|                 |                                                 |                                                              |
|                 |                                                 |                                                              |



## 6 proc

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



## 7 swtch 

| CATEGORY       | FIELD/FUNCTION                                        | 作用                                                         |
| -------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| PUBLIC FUCTION | void swtch(struct context **old, struct context *new) | 把当前执行上下文相关寄存器推到当前栈上，构成了一个struct context，并把该struct context地址保存到*old。最后把new中保存的寄存器值load到相应寄存器中。换句话说该函数的功能是：把当前执行上下文保存到old，并切换到new定义的执行上下文继续执行 |
|                |                                                       |                                                              |
|                |                                                       |                                                              |



## 8 picirq

禁用PIC，xv6使用APIC




## 9 lapic

| CATEGORY        | TYPE/FIELD/FUNCTION  | 作用                                                       |
| --------------- | -------------------- | ---------------------------------------------------------- |
| FIELD           | volatile uint *lapic | local APIC 寄存器Map到的内存地址，每一个逻辑核该地址都相同 |
| --              | --                   | --                                                         |
| PUBLIC FUNCTION | int lapicid(void)    | 通过读取 local APIC 寄存器获取当前正在执行CPU ID           |
| PUBLIC FUNCTION | void seginit(void)   | ?                                                          |
| PUBLIC FUNCTION | void  lapiceoi(void) | 发送EOI:Acknowledge interrupt.                             |
|                 |                      |                                                            |
|                 |                      |                                                            |



## 10 ioapic

| CATEGORY         | TYPE/FIELD/FUNCTION                    | 作用                                                         |
| ---------------- | -------------------------------------- | ------------------------------------------------------------ |
| PRIVATE FUNCTION | uint ioapicread(int reg)               | 读取IOAPIC寄存器中的数据                                     |
| PRIVATE FUNCTION | ioapicwrite(int reg, uint data)        | 把data写入IOAPIC寄存器                                       |
| --               | --                                     | --                                                           |
| PUBLIC FUNCTION  | void ioapicinit(void)                  | 初始化ioapic，初始状态：Mark all interrupts edge-triggered, active high, disabled, and not routed to any CPUs |
| PUBLIC FUNCTION  | void ioapicenable(int irq, int cpunum) | Mark interrupt edge-triggered, active high, enabled, and routed to the given cpunum |



## 11 trap

| CATEGORY        | FIELD/FUNCTION             | 作用                                                     |
| --------------- | -------------------------- | -------------------------------------------------------- |
| FIELD           | vectors                    | 由vectors.pl生成，几号中断就由vectors[i]中断处理程序处理 |
| PUBLIC FUNCTION | tvinit(void)               | 初始化中断处理向量                                       |
| PUBLIC FUNCTION | idtinit(void)              | 把中断处理向量载入idt寄存器                              |
| PUBLIC FUNCTION | trap(struct trapframe *tf) | Trap处理总入口                                           |
|                 |                            |                                                          |



## 12 ide

| CATEGORY         | TYPE/FIELD/FUNCTION          | 作用                                                         |
| ---------------- | ---------------------------- | ------------------------------------------------------------ |
| PUBLIC FUNCTION  | void ideintr(void)           | ide设备中断处理。中断一定是意味着请求队列的第一个请求执行完成：1） 通过port io完成相关读写；2） 请求队列第一个请求出队列；3） 向ide设备发起请求队列中的下一个请求（调用idestart） |
| PUBLIC FUNCTION  | void  iderw(struct buf *b)   | 1） 把struct buf加入到请求队列末尾；2） 如果这个新加入的请求已经是请求头了，则直接向ide设备发起请求（调用idestart）；3） sleep等待请求完成 |
| --               | --                           | --                                                           |
| PRIVATE FUNCTION | void idestart(struct buf *b) | 向ide设备发起请求,请求响应通过中断处理（ideintr）            |



## 13 bio

| CATEGORY         | TYPE/FIELD/FUNCTION                       | 作用                                                         |
| ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| FIELD            | bcache.buf                                | 所有的buffer cache块集合                                     |
| FIELD            | bcache.head                               | buffer cache块LRU队列，the first buffer in the list is the most recently used, and the last is the least recently used. The two loops in bget take advantage of this: the scan for an existing buffer must process the entire list in the worst case, but checking the most recently used buffers first (starting at bcache.head and following next pointers) will reduce scan time when there is good locality of reference. The scan to pick a buffer to reuse picks the least recently used buffer by scanning backward (following prev pointers). |
| --               | --                                        | --                                                           |
| PUBLIC  FUNCTION | binit(void)                               | 初始化bio（buffer cache）模块                                |
| PUBLIC  FUNCTION | struct buf* bread(uint dev, uint blockno) | 根据给定的设备号和块号返回一块：现在buffer cache中查找，若找到直接返回，若没有找到则通过调用ide接口从磁盘中读取，放入buffer cache，并返回。Once bread has read the disk (if needed) and returned the buffer to its caller, the caller has exclusive use of the buffer and can read or write the data bytes. If the caller does modify the buffer, it must call bwrite to write the changed data to disk beforereleasing the buffer. |
| PUBLIC  FUNCTION | void bwrite(struct buf *b)                | 把给定的块写入到磁盘                                         |
| PUBLIC  FUNCTION | brelse(struct buf *b)                     | 访问完一块后释放上面的锁，并将其移动到Most Recently Used队首 |
| --               | --                                        | --                                                           |
| PRIVATE FUNCTION | struct buf*bget(uint dev, uint blockno)   | 根据给定的设备号和块号在buffer cache中查找，如果不在buffer cache中则分配一块buffer cache空间用于存放。If all the buffers are busy, then too many processes are simultaneously executing file system calls; bget panics. A more graceful response might be to sleep until buffer became free, though there would then be a possibility of deadlock. |
|                  |                                           |                                                              |
|                  |                                           |                                                              |



## 14 log

| CATEGORY         | TYPE/FIELD/FUNCTION      | 作用                                                         |
| ---------------- | ------------------------ | ------------------------------------------------------------ |
| PUBLIC FUNCTION  | initlog(int dev)         | 初始化log模块，读取super block，获取log存储offset，并从存储的日志恢复 |
| PUBLIC FUNCTION  | begin_op(void)           | 在每一个文件系统模块系统调用开始前调用：waits until the logging system is not currently committing, and until there is enough unreserved log space to hold the writes from this call. |
| PUBLIC FUNCTION  | end_op(void)             | called at the end of each FS system call: decrements the count of outstanding system calls. If the count is now zero, it commits the current transaction by calling commit(). |
| PUBLIC FUNCTION  | log_write(struct buf *b) | 把块写入日志，对块的写操作不直接访问bio层，而是通过log_write。这边接口不保持对称，通常的操作模式是通过bread()读取数据块，如果需要修改，通过log模块进行写入。实际的落盘操作在end_op()中执行，期间的多个块的写入被包装成一个“事务”。 |
| --               | --                       | --                                                           |
| PRIVATE FUNCTION | write_log(void)          | 把日志队列中所有修改过的块依次从bio层的cache中读取出来（log队列中只记录块号），调用bwrite写入磁盘log区域 |
| PRIVATE FUNCTION | install_trans(void)      | 把日志区中的数据块依次读取出来，写入实际位置                 |
| PRIVATE FUNCTION | write_head(void)         | 写入日志头，包含字段：日志中总块数，和每个块号。This is the true point at which the current transaction commits. |
| PRIVATE FUNCTION | void commit()            | 分几步：1）调用write_log()把数据落盘到日志区；2）调用write_head()这一步是原子的，到此当前transaction commit成功；3)调用install_trans()把日志区中的数据写入到真正磁盘位置；4)把日志头中的长度字段置位0，再次复写日志头，至此日志清理成功 |
| PRIVATE FUNCTION | read_head(void)          | 读取日志头                                                   |
| PRIVATE FUNCTION | recover_from_log(void)   | 从日志中恢复：读取日志头，如果长度不为0，认为有已commit但是没有install的数据，调用install_trans()进行install，最后把日志头长度置位0并复写日志头（清理日志） |



## 15 fs

| CATEGORY         | TYPE/FIELD/FUNCTION                                          | 作用                                                         |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| TYPE             | struct dinode                                                | 表示一个存储在磁盘上的inode, An inode describes a single unnamed file. |
| TYPE             | struct inode                                                 | 内存中管理inode所需的结构，重要字段：1) int ref：counts the number of C pointers referring to the in-memory inode, and the kernel discards the inode from memory if the reference count drops to zero.;2)short nlink: (on disk and copied in memory if it is cached) that counts the number of directory entries that refer to a file; xv6 won’t free an inode if its link count is greater than zero. |
| TYPE             | struct dirent                                                | 表示一个目录项。目录（Directory）被实现为一种特殊的文件，inode类型为T_DIR，磁盘上的inode中存储的是struct dirent数组，包含两个字段：name和对应的inode号 |
|                  |                                                              |                                                              |
| --               | --                                                           | --                                                           |
| FIELD            | icache                                                       | The kernel keeps the set of active inodes in memory          |
|                  |                                                              |                                                              |
| --               | --                                                           | --                                                           |
| PUBLIC FUNCTION  | ilock(struct inode *ip)                                      | 加锁指定的inode，如果inode对应的磁盘数据还没有读取，则在此时读取 |
| PUBLIC FUNCTION  | iunlock(struct inode *ip)                                    | 解锁指定的inode                                              |
| PUBLIC FUNCTION  | iupdate(struct inode *ip)                                    | 把修改过的inode写入磁盘。inode任何字段修改后都必须调用本方法。 |
| PUBLIC FUNCTION  | iunlockput(struct inode *ip)                                 | iunlock+iput                                                 |
| PUBLIC FUNCTION  | struct inode* ialloc(uint dev, short type)                   | 遍历磁盘上的inode区，寻找空闲的inode，并通过调用iget返回。Mark it as allocated by  giving it type type. Returns an unlocked but allocated and referenced inode. |
| PUBLIC FUNCTION  | struct inode*dirlookup(struct inode *dp, char *name, uint *poff) | 在给定的目录中搜索给定名字的文件或目录，返回其对应的inode    |
| PUBLIC FUNCTION  | dirlink(struct inode *dp, char *name, uint inum)             | 把给定的文件或目录（由name和inode号定义）加入到给定的目录中  |
| PUBLIC FUNCTION  | int readi(struct inode *ip, char *dst, uint off, uint n)     | 读取inode所指向文件的off偏移量处，n字节长度数据到dst         |
| PUBLIC FUNCTION  | int writei(struct inode *ip, char *src, uint off, uint n)    | 把src处开始，n字节数据写入到给定inode所指向文件的off偏移量处。如果写入位置超出文件总长度，则对文件长度进行扩展 |
| PUBLIC FUNCTION  | stati(struct inode *ip, struct stat *st)                     | 从inode中读取元数据                                          |
| PUBLIC FUNCTION  | inode*namei(char *path)                                      | 根据path查找对应的inode                                      |
| PUBLIC FUNCTION  | struct inode* nameiparent(char *path, char *name)            | 查找整个path的最后一级目录对应的inode                        |
| --               | --                                                           | --                                                           |
| PRIVATE FUNCTION | balloc(uint dev)                                             | 读取空闲块位图，查找空闲的块返回                             |
| PRIVATE FUNCTION | bfree(int dev, uint b)                                       | 释放使用的磁盘块                                             |
| PRIVATE FUNCTION | uint bmap(struct inode *ip, uint bn)                         | Return the disk block address of the nth block in inode ip. If there is no such block, allocates one.If the block number exceeds NDIRECT+NINDIRECT, bmap panics; writei contains the check that prevents this from happening. |
| PRIVATE FUNCTION | struct inode iget(uint dev, uint inum)                       | 根据设备号和inode号查找一个inode，如果已经在icache中直接返回，否则分配一个空的inode（不从磁盘中读取实际的inode字段）返回 |
| PRIVATE FUNCTION | iput(struct inode *ip)                                       | Drop a reference to an in-memory inode. If that was the last reference, the inode cache entry can be recycled. If that was the last reference and the inode has no links to it, free the inode (and its content) on disk. All calls to iput() must be inside a transaction in case it has to free the inode. |
| PRIVATE FUNCTION | itrunc(struct inode *ip)                                     | truncate the file to zero bytes, freeing the data blocks; sets the inode type to 0 (unallocated); and writes the inode to disk |
| PRIVATE FUNCTION | char* skipelem(char *path, char *name)                       | 跳过一个path element，输入一个path，把path中的下一个element拷贝到name中，返回值是指向下一个path element开头的指针。如果返回值是0，则path中的element已经遍历完毕 |
| PRIVATE FUNCTION | inode*namex(char *path, int nameiparent, char *name)         | 根据path查找对应的inode，If parent != 0, return the inode for the parent and copy the final path element into name |



## 16 file

| CATEGORY        | TYPE/FIELD/FUNCTION                              | 作用                                                        |
| --------------- | ------------------------------------------------ | ----------------------------------------------------------- |
| TYPE            | struct devsw                                     | 包含两个函数指针read和write，用于不同设备注册不同的读写函数 |
| TYPE            | struct file                                      | 封装一个打开的文件                                          |
| --              | --                                               | --                                                          |
| FIELD           | struct devsw devsw[]                             | 每个设备注册一个struct devsw，也就是读写函数                |
| FIELD           | ftable                                           | 管理全局所有打开的文件                                      |
| --              | --                                               | --                                                          |
| PUBLIC FUNCTION | fileinit(void)                                   | 初始化file模块                                              |
| PUBLIC FUNCTION | struct file* filealloc(void)                     | 分配一个可用的struct file结构                               |
| PUBLIC FUNCTION | struct file* filedup(struct file *f)             | Increment ref count for file f                              |
| PUBLIC FUNCTION | fileclose(struct file *f)                        | Decrement ref count, close when reaches 0.                  |
| PUBLIC FUNCTION | filestat(struct file *f, struct stat *st)        | 从文件的inode中读取相关元信息                               |
| PUBLIC FUNCTION | int fileread(struct file *f, char *addr, int n)  | 从打开的文件中读取数据（调用inode层接口）                   |
| PUBLIC FUNCTION | int filewrite(struct file *f, char *addr, int n) | 向打开的文件中写入数据                                      |



## 17 pipe

| 函数                                              | 作用                                            |
| ------------------------------------------------- | ----------------------------------------------- |
| int pipealloc(struct file **f0, struct file **f1) |                                                 |
| pipeclose(struct pipe *p, int writable)           |                                                 |
| int pipewrite(struct pipe *p, char *addr, int n)  | 把给定地址的n字节数据写入到pipe                 |
| int piperead(struct pipe *p, char *addr, int n)   | 把pipe中的数据读取到指定的缓冲区，最多读取n字节 |
|                                                   |                                                 |












~