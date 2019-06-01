## disk partition

Disk partitioning or disk slicing[1] is the creation of one or more regions on secondary storage, so that each region can be managed separately.[2] These regions are called partitions. It is typically the first step of preparing a newly installed disk, before any file system is created. The disk stores the information about the partitions' locations and sizes in an area known as the partition table that the operating system reads before any other part of the disk.

磁盘分区上创建文件系统，文件系统在创建时被分为3部分：

1. 超级块：存储整个文件系统的状态
2. inode区
3. 数据块区:存储文件数据



## inode 和 dentry
A filesystem is represented in memory using dentries and inodes.  Inodes are the objects that represent the underlying files (and also directories).  A dentry is an object with a string name (d_name), a pointer to an inode (d_inode), and a pointer to the parent dentry (d_parent).

So a tree such as

```
	/
	|
	foo
	|   \
	bar  bar2
```
- is represented by four inodes: one each for foo, bar, and bar2, and the root; 
- and three dentries: one linking bar to foo, one linking bar2 to foo, and one linking foo to the root.  
  The first of those dentries, for example, has a name of "bar", a d_inode pointing to the underlying file bar, and a d_parent pointing to the dentry for foo (which in turn would have a d_parent pointing to the dentry for the root).  The root dentry has a d_parent that points to itself.



## symbol link

A symbolic link contains a text string that is automatically interpreted and followed by the operating system as a path to another file or directory. This other file or directory is called the "target". The symbolic link is a second file that exists independently of its target. If a symbolic link is deleted, its target remains unaffected. If a symbolic link points to a target, and sometime later that target is moved, renamed or deleted, the symbolic link is not automatically updated or deleted, but continues to exist and still points to the old target, now a non-existing location or file. Symbolic links pointing to moved or non-existing targets are sometimes called broken, orphaned, dead, or dangling.

## hard link

Mapping from dentries to inodes given by d_inode is in general a many-to-one mapping; a single file may be pointed to by multiple paths in the same filesystem (called "hard links"), in which case it will not be deleted as long as any such path exists.

Files and directories may also be opened by processes, of course, and a struct file is used to represent this.  The struct file contains a pointer to the dentry.  The underlying file will also not be deleted as long as there are processes holding the file open, even though that file may no longer be accessible by any path in the filesystem.

## 文件读写的几种方式

### Buffered IO

- 缓冲 I/O，是指利用标准库缓存来加速文件的访问，而标准库内部再通过系统调用访问文件;
- 非缓冲 I/O，是指直接通过系统调用来访问文件，不再经过标准库缓存。

### Direct IO
- 直接 I/O，是指跳过操作系统的页缓存，直接跟文件系统交互来访问文件。
- 非直接 I/O 正好相反，文件读写时，先要经过系统的页缓存，然后再由内核或额外的系统调用，真正写入磁盘。
  想要实现直接 I/O，需要你在系统调用中，指定 O_DIRECT 标志。如果没有设置过，默认的是非
  直接 I/O。

### Blocked IO
设置 O_NONBLOCK 标志，就表示用非阻塞方式访问；而如果不做任何设置，默认的就是阻塞访问。

### Async IO
举个例子，在操作文件时，如果你设置了 O_SYNC 或者 O_DSYNC 标志，就代表同步 I/O。如果设置了 O_DSYNC，就要等文件数据写入磁盘后，才能返回；而 O_SYNC，则是在 O_DSYNC基础上，要求文件元数据也要写入磁盘后，才能返回。
再比如，在访问管道或者网络套接字时，设置了 O_ASYNC 选项后，相应的 I/O 就是异步 I/O。这样内核会再通过SIGIO 或者SIGPOLL 来通知进程文件是否可读写