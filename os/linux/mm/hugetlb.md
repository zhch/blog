## 什么是huge page

比传统4K大的页，通常是2M或1G。好处：

- Decreased page table overhead
- Relief of TLB pressure
- Not swappable. Therefore there is nopage-in/page-out mechanism overhead.HugePages are universally regarded as pinned.

### hugetlb
hugetlb 是TLB中的一个entry，其指向HugePage。 HugePage 通过hugetlb entries来实现，我们也可以说HugePage 是hugetlb page entry的一个句柄。 二者是几乎是可以换用的概念。

### hugetlbfs
A new in-memory filesystem like tmpfs and is presented by 2.6 kernel. Pages allocatedon hugetlbfs type filesystem are allocated in HugePages. 使用huge page时可以mmap hugetlbfs上的文件或者是MAP_ANONYMOUS | MAP_HUGETLB。



## 如何使用

需要事先通过系统管理员配置：

- https://www.cyberciti.biz/tips/linux-hugetlbfs-and-mysql-performance.html
- https://www.kernel.org/doc/Documentation/vm/hugetlbpage.txt

过去使用大页面内存主要透过hugetlbfs需要mount文件系统到某个点去，部署起来很不方便,我们只想要点匿名页面，要搞的那么麻烦吗？2.6.32内核开始通过支持MAP_HUGETLB方式来使用内存,避免了烦琐的mount操作，对用户更友好。

### 例子
（http://blog.yufeng.info/archives/2118）

#### 准备环境
```bash
# uname -a
Linux dr4000 2.6.32-131.17.1.el6.x86_64 #1 SMP Wed Oct 5 17:19:54 CDT 2011 x86_64 x86_64 x86_64 GNU/Linux
 
# cat /proc/meminfo |grep -i huge
AnonHugePages:      2048 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
 
# sysctl vm.nr_hugepages=192
vm.nr_hugepages = 192
 
# cat /proc/meminfo |grep -i huge
AnonHugePages:      2048 kB
HugePages_Total:     192
HugePages_Free:      192
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
```

从上面输出可以看到我们的内核是2.6.32, 我用的是RHEL 6U2发行版本，同时保留了192*2M页面。

#### 应用程序使用
```c
int main(int argc, char *argv[]) {
	char *m;
  	size_t s = (8UL * 1024 * 1024);
 
	m = mmap(
  		NULL, s, PROT_READ | PROT_WRITE,
		MAP_PRIVATE | MAP_ANONYMOUS | MAP_HUGETLB, 
		-1, 0
	);
	if (m == MAP_FAILED) {
		perror("map mem");
		m = NULL;
        return 1;
	}
 
	memset(m, 0, s);
 
	printf("map_hugetlb ok, press ENTER to quit!\n");
	getchar();
 
	munmap(m, s);
	return 0;
}

# gcc huge.c
# ./a.out
map_hugetlb ok, press ENTER to quit!
```

#### 如何验证
我们成功用大页面申请了8M内存，4个大页面，同时进行清零操作成功，再munmap之前，我们需要确认内存确实是被我们使用了:

```
# cat /proc/meminfo |grep -i huge
AnonHugePages:      2048 kB
HugePages_Total:     192
HugePages_Free:      188
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
```

 确实分配成功了4个huge page。




## 什么是transparent huge page

标准大页管理是预分配的方式，而透明大页管理则是动态分配的方式。便于用户“透明”的使用大页。但是目前存在一些问题，关闭方式：

```bash
echo never > /sys/kernel/mm/transparent_hugepage/enabled
```






