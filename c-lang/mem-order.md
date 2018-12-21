# C11内存模型

## 1 什么是C11内存模型
The memory model is the crux of the concurrency semantics of shared-memory systems.

It defines the possible values that a read operation is allowed to return for any given set of
write operations performed by a concurrent program, thereby defining the basic semantics
of shared variables.

It is impossible to meaningfully reason about a program or any part of the programming language implementation without an unambiguous memory model.

Conversely, the memory model also defines which instruction reorderings may be permitted, either by the processor, the memory system, or the compiler.

## 2 基础概念
### 2.1 2个Model
* Language memory model: Defines the optimizations, memory access re-writes and
  reorderings a compiler is allowed to perform when transforming program into code.
* Hardware memory model: Defines the optimizations and memory access reorderings
  a specific hardware architecture is allowed to perform.

### 2.2 4个order
* Source code order: The order in which the memory operations are specified by the
  source code by the programmer.
* Program order: The order in which the memory operations are specified in the
  machine code that is executed by the CPU. Note that this can differ from the source
  code order, because depending on the definition of the language memory model,
  compilers are allowed to reorder instructions as part of the optimization process.
* Execution order: The order in which the individual memory-reference instructions
  are executed on a given CPU. The execution order can differ from the compiled
  order due to optimizations based on the hardware memory model of the specific
  CPU-implementation.
* Perceived order: The order in which a CPU perceives its and other CPUs' memory
  operations. The perceived order can differ from the execution order due to caching,
  interconnect and memory-system optimizations. Different CPUs can perceive the same
  set of memory operations as occurring in different orders. This is also defined by the
  hardware memory model.

### 2.3 最自然的多线程程序语义：Sequential consistency
A natural view of the execution of a multi-threaded program is as follows. For each
step one of the threads is randomly chosen and the next step in that thread's execution (in
say, the program or the compiled order) gets executed. This process is repeated until the
program as a whole terminates. This is effectively equivalent to taking all the steps of all
threads in (program or compiled) order, and interleaving them in some way, resulting in a
single total order of all steps. No reordering of the thread's steps is permitted. Therefore,
whenever an object is accessed, the last value stored to the object in this order is retrieved.
An execution that can be understood as such an interleaving is referred to as sequentially
consistent.

Many results on concurrent algorithms or data structures either explicitly oder implicitly assume a sequentially consistent memory model.

### 2.4 Sequence-before
https://en.cppreference.com/w/cpp/language/eval_order

### 2.5 Happens-before
Let A and B represent operations performed by a multi-threaded process. If A happens-before B, then the memory effects of A effectively become visibleto the thread performing B before B is performed.

A happens-before order between two operations from the same thread (source code
order) is implicitly given by the sequenced-before order. A happens-before order between two operations from different threads (in the standard this is referred to as inter-thread-happens-before) must be established using C11 atomic operations.

## 3 x86提供的基础设施
none of todays processor architectures provide a fully sequentially consistent memory model.

x86-TSO (Total Store Order)模型：

![](https://user-images.githubusercontent.com/1244560/50348859-f407ae00-0574-11e9-9802-00312af468cb.jpeg)

the hardware threads interact with a storage subsystem represented by the dotted box. The storage subsystem comprises a shared memory that maps addresses to values, a global lock to indicate when a particular hardware thread has exclusive access to memory, and one store buer per hardware thread.

* The store buffers are FIFO and a reading thread must read its own most recent buffered write, if there is one, to that address. Otherwise reads are satisfied from shared memory.
* An mfence instruction flushes the store buffer of that thread.
* To execute a lock'd instruction, a thread must first acquire the global lock. At the end of the instruction, it flushes its store buffer and releases the lock. While the lock is held by one thread, no other thread can read. This essentially means that lock'd instructions enforce sequential consistency.
* A buffered write from a thread can propagate to the shared memory at any time except when some other thread holds the lock.

## 4 C11内存模型
在处理器基础设施的上层，C11内存模型提供屏蔽底层细节的通用内存模型。The memory model defines when multiple threads may access the same memory location, and specifies when updates by one thread become visible to other threads.

### 4.1 Atomic Operations
以gcc为例，提供下列atomic operaions：https://gcc.gnu.org/onlinedocs/gcc-8.2.0/gcc/_005f_005fatomic-Builtins.html#g_t_005f_005fatomic-Builtins

### 4.2 Memory Order
抽象如下6中Memory Order：

```c
enum memory_order {
    memory_order_relaxed,
    memory_order_consume,
    memory_order_acquire,
    memory_order_release,
    memory_order_acq_rel,
    memory_order_seq_cst
};
```

| Memory Order         | Explanation                              |
| -------------------- | ---------------------------------------- |
| memory_order_relaxed | Relaxed operation: there are no synchronization or ordering constraints imposed on other reads or writes, only this operation's atomicity is guaranteed |
| memory_order_consume | A load operation with this memory order performs a *consume operation* on the affected memory location: no reads or writes in the current thread dependent on the value currently loaded can be reordered before this load. Writes to data-dependent variables in other threads that release the same atomic variable are visible in the current thread. On most platforms, this affects compiler optimizations only |
| memory_order_acquire | A load operation with this memory order performs the *acquire operation* on the affected memory location: no reads or writes in the current thread can be reordered before this load. All writes in other threads that release the same atomic variable are visible in the current thread |
| memory_order_release | A store operation with this memory order performs the *release operation*: no reads or writes in the current thread can be reordered after this store. All writes in the current thread are visible in other threads that acquire the same atomic variable (see [Release-Acquire ordering](https://en.cppreference.com/w/c/atomic/memory_order#Release-Acquire_ordering) below) and writes that carry a dependency into the atomic variable become visible in other threads that consume the same atomic |
| memory_order_acq_rel | A read-modify-write operation with this memory order is both an *acquire operation* and a *release operation*. No memory reads or writes in the current thread can be reordered before or after this store. All writes in other threads that release the same atomic variable are visible before the modification and the modification is visible in other threads that acquire the same atomic variable. |
| memory_order_seq_cst | A load operation with this memory order performs an *acquire operation*, a store performs a *release operation*, and read-modify-write performs both an *acquire operation* and a *release operation*, plus a single total order exists in which all threads observe all modifications in the same order |
|                      |                                          |

A happens-before relationship can be established by using the following combinations of memory orders:

* memory_order_seq_cst and memory_order_seq_cst
* memory_order_release and memory_order_acquire
* memory_order_release and memory_order_consume

### 4.3 Fences
Another synchronization operation that can be used to establish a happens-before relation
is a fence:

```
extern "C" void atomic_thread_fence( std::memory_order order ) noexcept;
```

#### 4.3.1 Fence-Release 与Atomic-Acquire之间的同步
A release fence F in thread A synchronizes-with atomic acquire operation Y in thread B, if:

* there exists an atomic store X (with any memory order)
* Y reads the value written by X (or the value would be written by release sequence headed by X if X were a release operation)
* F is sequenced-before X in thread A

In this case, all non-atomic and relaxed atomic stores that are sequenced-before F in thread A will happen-before all non-atomic and relaxed atomic loads from the same locations made in thread B after Y.

#### 4.3.2 Atomic-Release与Fence-Acquire之间的同步
An atomic release operation X in thread A synchronizes-with an acquire fence F in thread B, if

* there exists an atomic read Y (with any memory order)
* Y reads the value written by X (or by the release sequence headed by X)
* Y is sequenced-before F in thread B

In this case, all non-atomic and relaxed atomic stores that are sequenced-before X in thread A will happen-before all non-atomic and relaxed atomic loads from the same locations made in thread B after F.

#### 4.4.3 Fence-Release与Fence-Acquire之间的同步
A release fence FA in thread A synchronizes-with an acquire fence FB in thread B, if

* There exists an atomic object M,
* There exists an atomic write X (with any memory order) that modifies M in thread A
* FA is sequenced-before X in thread A
* There exists an atomic read Y (with any memory order) in thread B
* Y reads the value written by X (or the value would be written by release sequence headed by X if X were a release operation)
* Y is sequenced-before FB in thread B

In this case, all non-atomic and relaxed atomic stores that are sequenced-before FA in thread A will happen-before all non-atomic and relaxed atomic loads from the same locations made in thread B after FB


## References
1. Memory Models for C/C++ Programmers [PDF]
2. https://gcc.gnu.org/onlinedocs/gcc-8.2.0/gcc/_005f_005fatomic-Builtins.html#g_t_005f_005fatomic-Builtins
3. https://en.cppreference.com/w/cpp/atomic/memory_order
4. https://en.cppreference.com/w/cpp/language/eval_order
5. https://en.cppreference.com/w/cpp/atomic/atomic_thread_fence