## 硬中断和软中断

Linux将中断处理过程分成了两个阶段：

- Top half（中断、硬中断、interrupt）：用来快速处理中断，在关闭中断的模式下运行，主要处理跟硬件紧密相关的或时间敏感的工作；
- Bottom half：属于不那么紧急需要处理的事情。在执行bottom half的时候，是开中断的。有多种bottom half的机制，例如：softirq（软中断）、tasklet、workqueue

每个CPU上都有一个软中断处理内核线程，命名规则为：ksoftirqd/<CPU编号>

