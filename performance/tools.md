# 工具

## A. CPU
### A.1. mpstat
#### A.1.1 What
mpstat 是一个常用的多核 CPU 性能分析工具，用来实时查看每个 CPU 的性能指标，以及
所有 CPU 的平均指标。

#### A.1.2常用 1line
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



## B. 进程
### B.1. pidstat
#### B.1.1 What
进程性能分析工具，用来实时查看进程的 CPU、内存、I/O 以及上下文切换等性能指标。

#### B.1.2 常用 1line

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









![](https://user-images.githubusercontent.com/1244560/50539394-9e598400-0bba-11e9-90e9-d866fb7c3c7b.png)