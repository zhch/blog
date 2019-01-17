# glibc 2.28 中 nptl(pthread) 各个同步工具的实现

## 1 spinlock

### 1.1 主要API

```C
#include <pthread.h>
int pthread_spin_lock(pthread_spinlock_t *lock);
int pthread_spin_trylock(pthread_spinlock_t *lock);
int pthread_spin_unlock(pthread_spinlock_t *lock);
```

### 1.2 实现原理
基于gcc原子操作atomic_compare_exchange_weak_acquire和nop while实现。

### 1.3 核心源码位置
nptl/pthread_spin_lock.c



## 2 mutex

### 2.1 主要API

```C
#include <pthread.h>
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex); 
```

### 2.2 实现原理
基于Linux Futex。

### 2.3 核心源码位置
nptl/pthread_mutex_lock.c



## 3 读写锁rwlock

### 3.1 API

```C
#include <pthread.h>
#include <time.h>
int pthread_rwlock_timedrdlock(pthread_rwlock_t *restrict rwlock, const struct timespec *restrict abs_timeout); 
int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);
int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock); 
int pthread_rwlock_timedwrlock(pthread_rwlock_t *restrict rwlock, const struct timespec *restrict abs_timeout); 
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock);
int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock); 
int pthread_rwlock_unlock(pthread_rwlock_t *rwlock); 
```

### 3.2 实现原理
基于futex+gcc原子操作atomic_load_relaxed ... 实现

### 3.3 核心源码位置
nptl/pthread_mutex_lock.c



## 4 Conditional Variable

### 4.1 主要API

```C
#include <pthread.h>
int pthread_cond_timedwait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex, const struct timespec *restrict abstime);
int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex); 
int pthread_cond_broadcast(pthread_cond_t *cond);
int pthread_cond_signal(pthread_cond_t *cond); 
```

### 4.2 实现原理
CV在使用的时候就需要借助mutex：

```C
pthread_mutex_lock(&qlock);    /*lock*/
pthread_cond_wait(&qready, &qlock); /*block-->unlock-->wait() return-->lock*/
pthread_mutex_unlock(&qlock); /*unlock*/
```
所以CV的实现基于mutex，也是基于Linux futex的。

### 4.3 核心源码位置

nptl/pthread_cond_wait.c



## 5 Semaphore

### 5.1 主要API

```C
#include <semaphore.h>
int sem_init(sem_t *sem, int pshared, unsigned int value);
int sem_destroy(sem_t *sem);
int sem_wait(sem_t *sem);
int sem_trywait(sem_t *sem);
int sem_timedwait(sem_t *sem, const struct timespec *abs_timeout);
int sem_post(sem_t *sem);
```

### 5.2 实现原理
基于futex和gcc原子操作实现

### 5.3 核心源码位置

nptl/sem_waitcommon.c



## 6 Barrier

### 6.1 主要API

```C
#include <pthread.h>
int pthread_barrier_init(pthread_barrier_t *restrict barrier, const pthread_barrierattr_t *restrict attr, unsigned count); 
int pthread_barrier_destroy(pthread_barrier_t *barrier);
int pthread_barrier_wait(pthread_barrier_t *barrier); 
```


### 6.2 实现原理
基于gcc原子操作和futex实现


### 6.3 核心源码位置

nptl/pthread_barrier_wait.c

