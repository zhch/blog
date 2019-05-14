## Overview

![](https://user-images.githubusercontent.com/1244560/57602635-3f594200-7592-11e9-8a5d-a7c4153acfe2.jpg)

## Buddy

- Suppose we have 16K to manage. It starts as one large block
- Now, we have a request A for a block of 3.6k. We round up to 4K and perform two splits to create such a block.
- Of course, nodes are merged when possible. Suppose the a allocation is freed
- However, it is only possible to join nodes which were previously split.

Example：<http://sandbox.mc.edu/~bennet/cs404/outl/buddy.html>



## Slab
仅用于内核。在Linux中，伙伴系统（buddy system）是以页为单位管理和分配内存。但是现实的需求却以字节为单位，假如我们需要申请20Bytes，总不能分配一页吧！那岂不是严重浪费内存。那么该如何分配呢？slab分配器就应运而生了，专为小内存分配而生。slab分配器分配内存以Byte为单位。但是slab分配器并没有脱离伙伴系统。

Kernel modules and drivers often need to allocate temporary storage for non-persistent structures and objects, such as inodes, task structures, and device structures. These objects are uniform in size and are allocated and released many times during the life of the kernel. In earlier Unix and Linux implementations, the usual mechanisms for creating and releasing these objects were the **kmalloc()** and **kfree()** kernel calls.

However, these used an allocation scheme that was optimized for allocating and releasing pages in multiples of the hardware page size. For the small transient objects often required by the kernel and drivers, these page allocation routines were horribly inefficient, leaving the individual kernel modules and drivers responsible for optimizing their own memory usage.

One solution was to create a global kernel caching allocator which manages individual caches of identical objects on behalf of kernel modules and drivers. Each module or driver can ask the kernel cache allocator to create a private cache of a specific *object type*. The cache allocator handles growing each cache as needed on behalf of the module, and more importantly, the cache allocator can release unused pages back to the free pool in times when memory is in a crunch. The cache allocator works with the rest of the memory system to maintain a balance between the memory needs of each driver or module and the system as a whole.

As an example, assume a file-system driver wishes to create a cache of inodes that it can pull from. Through the kmem_cache_create() call, the slab allocator will calculate the optimal number of pages (in powers of 2) required for each slab given the inode size and other parameters. A kmem_cache_t pointer to this new inode cache is returned to the file-system driver.

When the file-system driver needs a new inode, it calls kmem_cache_alloc() with the kmem_cache_t pointer. The slab allocator will attempt to find a free inode object within the slabs currently allocated to that cache. If there are no free objects, or no slabs, then the slab allocator will grow the cache by fetching a new slab from the free page memory and returning an inode object from that.

When the file-system driver is finished with the inode object, it calls kmem_cache_free() to release the inode. The slab allocator will then mark that object within the slab as free and available.

If all objects within a slab are free, the pages that make up the slab are available to be returned to the free page pool if memory becomes tight. If more inodes are required at a later time, the slab allocator will re-grow the cache by fetching more slabs from free page memory. All of this is completely transparent to the file-system driver.

![<>](https://user-images.githubusercontent.com/1244560/57620490-7c85fa00-75bb-11e9-9047-3fffe0745807.gif)

### Slab着色：Cache Coloring of Slabs

Cache Coloring is a method to ensure that access to the slabs in kernel memory make the best use of the processor L1 cache. This is a performance tweak to try to ensure that we take as few cache hits as possible. Since slabs begin on page boundaries, it is likely that the objects within several different slab pages map to the same cache line, called 'false sharing'. This leads to less than optimal hardware cache performance. By offsetting each beginning of the first object within each slab by some fragment of the hardware cache line size, processor cache hits are reduced.

![](https://user-images.githubusercontent.com/1244560/57620606-dab2dd00-75bb-11e9-933a-396d3effb088.gif)

