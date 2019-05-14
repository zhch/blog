### Minor page faults

If the page fault handler running within the kernel is able to successfully map a physical page for the user-space virtual address that triggered the page-fault, this is known as minor page fault. Assuming that there is an abundant amount of physical memory freely available, accessing buffers allocated by **malloc()** would be satisfied by the Linux kernel via minor page faults.



### Major page faults

Major faults occur on one of the following possible occasions:

The page fault handler could be trying to reclaim physical pages that were swapped out earlier due to memory pressure (shortage of physical memory), thus incurring disk I/O in order to read from previously swapped-out page from the configured swap-space.
The page fault handler could be trying to read from an open file that was just mmap()'ed. This would also incur disk I/O if the file contents were already not within the page cache.