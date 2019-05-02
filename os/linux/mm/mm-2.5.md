## 主要层次结构

### Node
With large-scale machines(NUMA), memory may be arranged into banks that  incur a different cost to access depending on their distance from the processor. For example, a bank of memory might be assigned to each CPU, or a bank of memoryvery suitable for Direct Memory Access (DMA) near device cards might be assigned.Each bank is called a node. 

#### 管理数据结构

- 每个Node用 struct pglist_data(typedef成pg_data_t)描述
- 所有pg_data_t串成一个pgdat_list链表
- 对于UMA，存在一个唯一的pg_data_t结构，称作contig_page_data


### Zones
Each node is divided into a number of blocks called zones, which represent ranges within memory. Zones should not be confused with zone-based allocators because they are unrelated. 

Zone的类型有ZONE_DMA, ZONE_NORMAL和ZON_ HIGHMEM三种:

1. ZONE_DMA is memory in the lower physical memory ranges that certain Industry Standard Architecture (ISA) devices require. 

2. Memory within ZONE_NORMAL is directly mapped by the kernel into the upper region of the linear address space
3. ZONE_HIGHMEM is the remaining available memory in the system and is not directly mapped by the kernel.

With the x86, the zones are the following:

- ZONE_DMA First 16MiB of memory

- ZONE_NORMAL 16MiB - 896MiB
- ZONE_HIGHMEM 896 MiB - End

#### 管理数据结构
每个Zone通过数据结构struct zone_struct（typedef成zone_t）
zone_t的类型有ZONE_DMA, ZONE_NORMAL和ZONE_HIGHMEM三种

#### 计算Zone的大小
The size of each zone is calculated during setup_memory()。The PFN is an offset, counted in pages, within the physical memory map。需要获取三个值：

1. min_low_pfn: 表示The first PFN usable by the system,  is located at the beginning of the first page after _end, which is the end of the loaded kernel image.

2. max_pfn：表示the last page frame in the system, is calculated is quite architecture specific. In the x86 case, the function find_max_pfn() reads through the whole e820 map for the highest page frame. The value is also stored as a file scope variable in mm/bootmem.c. The e820 is a table provided by the BIOS describing what physical memory is available, reserved or nonexistent.
3. max_low_pfn: 表示the physical memory directly accessibleby the kernel and is related to the kernel/userspace split in the linear address space marked by PAGE_OFFSET.在内存较小的机器上max_pfn == max_low_pfn。

通过这3个值就可以Striaght-forward的计算arch/i386/mm/init.c#highstart_pfn和highend_pfn， The values are used later to initialize the high memory pages for the physical page allocator。


#### Zone空闲空间水位
When available memory in the system is low, the pageout daemon kswapd is woken
up to start freeing pages,每个Zone可以配置pages_low, pages_min和pages_high三个空闲空间水位，作用效果如下图所示：

![](https://user-images.githubusercontent.com/1244560/50680267-06d4b500-1042-11e9-8c8d-2fee74a72de2.png)

#### Zone Wait Queue Table
When I/O is being performed on a page, such as during page-in or page-out, the I/O is locked to prevent accessing it with inconsistent data. Processes that want to use it have to join a wait queue before the I/O can be accessed by calling wait_on_page().

Each page could have a wait queue, but it would be very expensive in terms of memory to have so many separate queues. Instead, the wait queue is stored in the zone_t. It is possible to have just one wait queue in the zone, but that would mean that all processes waiting on any page in a zone would be woken up when one was unlocked. This would cause a serious thundering herd problem. Instead, a hash table of wait queues is stored in zone_t->wait_table. In the event of a hash collision, processes may still be woken unnecessarily, but collisions are not expected to occur frequently. The function page_waitqueue() is responsible for returning which wait queue to use for a page in a zone. It uses a simple multiplicative hashing algorithm based on the virtual address of the struct page being hashed.

The maximum size it will be is 4,096 wait queues. For smaller tables, the size of the table is the minimum power of 2 required to store NoPages / PAGES_PER_WAITQUEUE number of queues, where NoPages is the number of pages in the zone and PAGE_PER_WAITQUEUE is defined to be 256.


### Pages
The system’s memory is comprised of fixed-size chunks called page frames. 

#### 管理数据结构
- 每一个physical page frame通过struct page描述

- 所有struct page结构都存在全局的mem_map数组中，which is usually stored at the beginning of ZONE NORMAL or just after the area reserved for the loaded kernel image in low memory machines.

