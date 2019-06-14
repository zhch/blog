# Log and recovery

## 1 Database Modification

We say a transaction modifies the database if it performs an update on a disk
buffer, or on the disk itself; updates to the private part of main memory do not
count as database modifications:

- deferred-modification: 直到事务提交时才真正Modify database
- immediate-modification：可能在事务运行期间就对Database进行Modify

### Database Modification与隔离性实现方式的典型关系

When either snapshot-isolation or validation is used for concurrency control, database updates of a transaction are (conceptually) deferred until the transaction is partially committed; the deferred-modification technique is a natural fit with these concurrency control schemes. However, it is worth noting that some implementations of snapshot isolation use immediate modification, but provide a logical snapshot on demand: when a transaction needs to read an item that a concurrent transaction has updated, a copy of the (already updated) item is made, and updates made by concurrent transactions are rolled back on the copy of the item. Similarly, immediate modification of the database is a natural fit with two-phase locking, but deferred modification can also be used with two-phase locking.

## 2 Log格式

Log由Log Record的顺序序列组成，最常用的Log Record实现格式可以描述为一个四元组：(T,X,V1,V2):

- T: 事务唯一id
- X: data item唯一id，最常用的实现是Page ID+Page内offset
- V1：data item在修改前的老值，也称before image
- V2：data item在修改后的新值,  也称after image

除此之外，还包含一些特殊用途的Log Record：诸如事务开始、事务提交、事务回滚、打Checkpoint等。


## 3 Recovery算法的几个基本前提

1. 支持immediate-modification，也即支持1）事务已经提交，但是事务包含的部分对数据库的修改还仅停留在Buffer Pool中，延迟落盘。2）支持事务在运行过程中还未提交，但已对数据库进行了修改，并且这些修改可能已经落盘，随后的数据库Crash需要回滚这些修改。

2. 避免级联回滚：The recovery algorithm described here requires that a data item that has been updated by an uncommitted transaction cannot be modified by any other transaction, until the first transaction has either committed or aborted. 也即要求调度算法避免cascading rollback（级联回滚）。


## 4 基本的Recovery算法

所有数据库操作可以规约为database modification、事务提交、事务回滚几个操作。

### 4.1 Database modification
对数据库做任何修改前必须保证响应的Log Record已经落盘。the system has available both the old value prior to the modification of the data item and the new value that is to be written for the data item.

### 4.2 Transaction Commit
事务提交时专门的Log Record: TxCommit(Ti)被写入磁盘。TxCommit日志是事务所有Log Record的最后一条。TxCommit日志被写入磁盘保证该事务之前所有的Log Record都已经写入磁盘。由此保证：

- 如果数据库在TxCommit完整写入磁盘之后Crash，则日志中已有足够信息保证事务所做所有操作可以redone。

- 如果数据库在TxCommit完整写入磁盘之前Crash（包括写入不完整），则日志中也有足够信息进行rollback

Thus, the output of the block containing the commit log record is the single atomic action that results in a transaction getting committed.2

### 4.3 Transaction Rollback

回滚事务Ti时执行流程如下：

1. 反向扫描Log，对于每一条关于Ti的Log Record：(Ti,Xj,V1,V2)：
   a. The value V1 is written to data item Xj
   b. 把专门的Log Record: RedoOnlyLog(Ti, Xj, V1)写入日志。These log records are sometimes called compensation log records. Such records do not need undo information, since we never need to undo such an undo operation. 
   
2. 当找到Log Record: TxStart(Ti)后停止反向扫描。

3. 把Log Record：TxRollback(Ti)写入日志

### 4.4 Check-point

这里介绍的是Fuzzy Check Point算法，相比较最基础的Check Point不需要在整个Check Point期间对数据库进行禁写。Fuzzy Check Point算法除了日志之外还需要在磁盘上固定位置记录LastCheckPoint。

具体执行流程如下：

1. 获得当前所有处于运行中状态的事务列表ActiveTxList

2. 获得Buffer Pool所有脏页列表，DirtyPageList

3. 把所有日志写入磁盘

4. 把DirtyPageList中的所有脏页写入磁盘

5. 写入专门的Log Record：CheckPoint(ActiveTxList)

6. 更新LastCheckPoint，保存最新写入的CheckPoint在日志中的Offset


数据库恢复时，通过LastCheckPoint找到CheckPoint开始恢复，而不是日志中的最后一个CheckPoint作为有效的CheckPoint开始恢复。

### 4.5 Database Recovery

故障后恢复过程可以分为redo和undo两个阶段。按顺序先执行redo再执行undo，undo完成后数据库就可以开始恢复服务。

Observe that the redo phase replays every log record since the most recent checkpoint record. In other words, this phase of restart recovery repeats all the update actions that were executed after the checkpoint, and whose log records reached the stable log. The actions include actions of incomplete transactions and the actions carried out to rollback failed transactions. The actions are repeated in the same order in which they were originally carried out; hence, this process is called repeating history. Although it may appear wasteful, repeating history even for failed transactions simplifies recovery schemes.

#### 4.5.1 redo阶段

具体执行流程如下：

1. 根据LastCheckPoint找到最新有效的CheckPoint

2. 创建需要被Roll Back的事务列表：UndoList，初始化为CheckPoint的ActiveTxList

3. 从CheckPoint开始正向扫描日志

4. 遇到的每一个Log Record：(Ti, Xj, V1,V2) 或者是 RedoOnlyLog(Ti, Xj, V2)都进行redo，也即在Buffer Pool中设置Xj的值为V2

5. 遇到的每个一个Log Record：TxStart(Ti)，把Ti加入到UndoList

6. 遇到的每一个Log Record：TxCommit(Ti) 或者是 TxRollBack(Ti)，把Ti从UndoList中移除

#### 4.5.2 undo阶段

具体执行流程如下：

1. 从日志末尾开始反向扫描

2. 遇到的每一条属于UndoList事务的Log Record：(Ti, Xj, V1, V2)都进行undo，也即在Buffer Pool中设置Xj的值为V1

3. 遇到的每一条属于UndoList事务的Log Record：TxStart(Ti)，在日志中写入TxRollback(Ti)，并把Ti从UndoList中移除

4. 当UndoList被清空之后Undo流程就执行完成了。至此数据库恢复过程完成。

## 5 Log Buffering

Log可以不必每次写入立即刷盘，可以通过Log Buffering优化写入性能。也即：将Log Record写入内存中的Buffer, 内存中的多条Log Record可以攒在一起在合适时机批量刷入磁盘。Log Buffering机制的实现中需要保证以下几点（也称WAL Rule）：

1. 事务Commit时保证TxCommit Log Record已经刷盘

2. TxCommit Log Record刷盘时保证同一事务之前所有的日志都已经刷盘

3. Buffer Pool中任何Page在刷盘前，任何与该Page相关的日志必须已经刷盘。(Strictly speaking, the WAL rule requires only that the undo information in the log has been output to stable storage, and it permits the redo information to be written later. The difference is relevant in systems where undo information and redo information are stored in separate log records.)

上述第3条的常用实现方式如下：

- 事务在对任何Data Item进行修改前，先在Buffer Pool中对Data Item所属的Page加锁。修改完成后立即释放相应锁，而不需要Hold直到事务结束（是一个Latch而非Lock）

- Buffer Pool在对任何Page进行刷盘时按照如下流程进行操作：
  1. 对Page进行加锁，这一步保证对同一Page进行的并发写被Block
  2. 对日志进行按顺序刷盘，直到保证属于该Page的所有日志都已经落盘
  3. 把Page进行刷盘
  4. 上述操作完成后立即释放Page上的锁


## 6 与B+树并发控制结合的优化

继续读完db-concepts-16.7 which depends on chapter 11

## 7 ARIES算法

是state of art的生产级算法。主要Points：

1. Uses a log sequence number (LSN) to identify log records, and stores LSNs in database pages to identifywhich operations have been applied to a database page.
2. Supports physiological redo operations, which are physical in that the affected page is physically identified, but can be logical within the page. For instance, the deletion of a record from a page may result in many other records in the page being shifted, if a slotted page structure (Section 10.5.2) is used. With physical redo logging, all bytes of the page affected by the shifting of records must be logged. With physiological logging, the deletion operation can be logged, resulting in a much smaller log record. Redo of the deletion operation would delete the record and shift other records as required.
3. Uses a dirty page table to minimize unnecessary redos during recovery. As mentioned earlier, dirty pages are those that have been updated inmemory, and the disk version is not up-to-date.
4. Uses a fuzzy-checkpointing scheme that records only information about dirty pages and associated information and does not even require writing

继续读完db-concepts-16.8

