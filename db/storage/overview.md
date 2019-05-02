### DB模块关系

自上而下DB几层模块关系：

- Query Planning
- Operator Execution
- Access Methods
- Buffer Pool Manager
- Disk Manager



### DB 存储层次关系

- Value Representation
- Tuple Layout
- Page Layout
- File Storage



### RAID,SAN和NAS

#### RAID (redundant arrays of independent disks)

Multiple disks ar organized using RAID to give the servers a logical view of a very large and very reliable disk.

#### SAN (storage area network)

Large numbers of disks are connected by a high-speed network to a number of server computers. The computer and the disk subsystem continue to use the SCSI, SAS, or Fiber Channel interface protocols to talk with each other, although they may be separated by a network. Remote access to disks across a storage area network means that disks can be shared by multiple computers that could run different parts of an application in parallel. Remote access also means that disks containing important data can be kept in a central server room where they can be monitored and maintained by system administrators, instead of being scattered in different parts of an organization.

#### NAS (Network attached storage)

NAS is much like SAN, except that instead of the networked storage appearing to be a large disk, it provides a file system interface using networked file system protocols such as NFS or CIFS.

### File Organization

- 一个数据库存储在一个到多个文件中
- 每个文件被分隔为定长的Block集合，最常见的Block长度为4~16KB
- 常见方案：每个Block包含一个或多个Record：Record长度不超过Block长度（超长字段独立文件存储）；一个Recored不会被割裂存储在多个Block中


