# Storage Overview

## DB模块关系

自上而下DB几层模块关系：

- Query Planning
- Operator Execution
- Access Methods
- Buffer Pool Manager
- Disk Manager

DB 存储层次关系:

- Value Representation
- Tuple Layout
- Block Layout
- File Storage


## RAID,SAN和NAS

### RAID (redundant arrays of independent disks)

Multiple disks ar organized using RAID to give the servers a logical view of a very large and very reliable disk.

### SAN (storage area network)

Large numbers of disks are connected by a high-speed network to a number of server computers. The computer and the disk subsystem continue to use the SCSI, SAS, or Fiber Channel interface protocols to talk with each other, although they may be separated by a network. Remote access to disks across a storage area network means that disks can be shared by multiple computers that could run different parts of an application in parallel. Remote access also means that disks containing important data can be kept in a central server room where they can be monitored and maintained by system administrators, instead of being scattered in different parts of an organization.

### NAS (Network attached storage)

NAS is much like SAN, except that instead of the networked storage appearing to be a large disk, it provides a file system interface using networked file system protocols such as NFS or CIFS.



## Tuple Layout

### 定长记录
实现较为简单，通常是一个NullBitMap+定长的AttributesArray。
NullBitMap的作用是可以在AttributesArray不用再用单独的字节表名Attribute是否为NULL，提高存储的紧凑度。如果NullBitMap中已经指明某个Attribute为NULL，则AttributesArray仍然将有该Attribute的位置，但是其中的数据将被忽略。

### 变长记录

通常格式为：NullBitMap+变长AttributePointerArray+定长AttributesArray。其中：

- 变长AttributePointerArray中每个元素是一个(offset, length)，对应记录中的每个变长Attribute的存储位置。
- NullBitMap和定长AttributesArray和定长记录的实现类似


## Block Layout

通常采用slotted-block(page) structure的方案：每个block按顺序分为三部分：

1. Header：记录当前Block中一共有几条记录，以及每条记录的Block内指针(offset, length)
2. Free space: Block内的空闲空间，可用于新插入的记录
3. Record Array：从Block的结束位置向开始位置反向存储（保证Free space位于Block的中部）

和这种存储方案相关的几个重要操作的实现：

- 当Block内的某条记录被删除时，直接在Header中做出标记即可
- 当某条记录被更新时，新的记录长度超过老记录长度时，可以直接把老记录标记为删除，并且在Free space中allocate新的空间
- Block内的碎片空间可以适时compaction


## File Organization

- 一个数据库存储在一个到多个文件中
- 每个文件被分隔为定长的Block集合，最常见的Block长度为4~16KB
- 每个Block包含一个或多个Record：Record长度不超过Block长度（超长字段独立文件存储，Record中只存储指向超长字段的指针）；一个Recored不会被割裂存储在多个Block中

### Heap File

### B+ Tree File

### Hash File

### Log Structure Merge Tree

