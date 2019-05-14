# 工具

## 速查

|           | CPU            | 中断     | 上下文切换                  | 内存                       | Swap   | 磁盘     | 网络     |      |      |
| --------- | -------------- | -------- | --------------------------- | -------------------------- | ------ | -------- | -------- | ---- | ---- |
| cachestat |                |          |                             | 全局Cache/buffer命中率     |        |          |          |      |      |
| cachetop  |                |          |                             | 进程Cache/buffer命中率     |        |          |          |      |      |
| dstat     | 全局util       | 全局次数 | 全局次数                    |                            | 换页数 | 读写带宽 | 读写带宽 |      |      |
| memleak   |                |          |                             | 内存泄漏检测               |        |          |          |      |      |
| mpstat    | 多核util       |          |                             |                            |        |          |          |      |      |
| pcstat    |                |          |                             | 文件在cache/buffer中的情况 |        |          |          |      |      |
| pidstat   | 各进程CPU Util |          | 进程voluntary/non voluntary |                            |        |          |          |      |      |
| sar       |                |          |                             | 使用情况，cache/buffer     | 换页数 |          |          |      |      |
| slabtop   |                |          |                             | slab使用情况               |        |          |          |      |      |
| vmstat    |                | 全局次数 | 全局次数                    | 使用情况，cache/buffer     | 换页数 |          |          |      |      |
|           |                |          |                             |                            |        |          |          |      |      |
|           |                |          |                             |                            |        |          |          |      |      |
|           |                |          |                             |                            |        |          |          |      |      |
|           |                |          |                             |                            |        |          |          |      |      |
|           |                |          |                             |                            |        |          |          |      |      |

- Also see the big picture at the bottom of this doc.



## mpstat

### What
mpstat 是一个常用的多核 CPU 性能分析工具，用来实时查看每个 CPU 的性能指标，以及
所有 CPU 的平均指标。

#### 常用 1line
```
$mpstat 1
# 间隔1秒连续输出

10:54:48 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:54:49 PM  all    0.90    0.00    0.03    0.00    0.00    0.00    0.00    0.00    0.00   99.06
10:54:50 PM  all    1.08    0.00    0.17    0.00    0.00    0.00    0.00    0.00    0.00   98.75
10:54:51 PM  all    1.45    0.00    0.40    0.00    0.00    0.00    0.00    0.00    0.00   98.16
```

```
mpstat -P ALL 1
# 输出所有处理器指标

10:56:26 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:56:27 PM  all    1.53    0.00    1.02    0.00    0.00    0.00    0.00    0.00    0.00   97.45
10:56:27 PM    0    2.02    0.00    1.01    0.00    0.00    0.00    0.00    0.00    0.00   96.97
10:56:27 PM    1    1.03    0.00    1.03    0.00    0.00    0.00    0.00    0.00    0.00   97.94
```



## pidstat
### What
进程性能分析工具，用来实时查看进程的 CPU、内存、I/O 以及上下文切换等性能指标。

### 重要参数
* -t: 统计中包含进程所有的线程，不加此参数时，有时会只统计进程本身的统计量，包含的线程没有涵盖。
* -w: 查看进程上下文切换情况

### 常用 1line

```
pidstat -u 5 1  
# 间隔 5 秒后输出一组数据，
# -u 表示 CPU 指标

11:00:30 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command
11:00:35 PM     0      5468    0.00    0.20    0.00    0.20    31  dockerd
11:00:35 PM 403582    105008    0.20    0.59    0.00    0.79    49  pidstat
11:00:35 PM  2916    120985   82.77    0.59    0.00   83.37     2  mysqld
11:00:35 PM 403582    183152    8.51    0.40    0.00    8.91    73  java
```

```
sudo pidstat -r   -p <pid> 1
# Report page faults and memory utilization.
# 间隔1秒输出一次

03:34:19 PM   UID       PID  minflt/s  majflt/s     VSZ    RSS   %MEM  Command
03:34:24 PM  2916    120985      0.20      0.00 277215708 4152188   0.79  mysqld
03:34:29 PM  2916    120985      0.20      0.00 277215708 4152188   0.79  mysqld
03:34:34 PM  2916    120985      0.20      0.00 277215708 4152188   0.79  mysqld
```

```
sudo pidstat -d  -p <pid> 1
# Report I/O statistics (kernels 2.6.20 and later only).

03:37:14 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
03:37:15 PM  2916    120985      0.00      0.00      0.00  mysqld
03:37:16 PM  2916    120985      0.00      0.00      0.00  mysqld
03:37:17 PM  2916    120985      0.00      0.00      0.00  mysqld
03:37:18 PM  2916    120985      0.00      0.00      0.00  mysqld
```



## vmstat

monitoring tool that collects and displays summary information about operating system memory, processes, interrupts, paging and block I/O. 

### 常用 1line

```
$vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  1      0 97296848 152092 30043036    0    0     0    45    0    0  1  0 99  0  0
 1  0      0 97294224 152092 30045416    0    0     0     0 17031 31370  3  1 97  0  0
 1  0      0 97291744 152092 30047868    0    0     0   220 17019 31372  3  1 97  0  0
```





## /proc文件系统



| /proc |             |                                   |      |      |      |      |      |      |      |
| ----- | ----------- | --------------------------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|       | /cpuinfo    | 各个CPU的明细信息                 |      |      |      |      |      |      |      |
|       | /interrupts | 各CPU上执行的各个中断(硬中断)次数 |      |      |      |      |      |      |      |
|       | /softirqs   | 各CPU上执行的各个软中断次数       |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |
|       |             |                                   |      |      |      |      |      |      |      |





![](https://user-images.githubusercontent.com/1244560/50539394-9e598400-0bba-11e9-90e9-d866fb7c3c7b.png)