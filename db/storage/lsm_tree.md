## Tiered Compaction

也叫size tiered compaction，RocksDB中叫Universal Compaction。

### 基本算法

最基本的流程如下：

1. memtables are periodically flushed to new sstables. These are pretty small, and soon their number grows.
2. As soon as we have enough (by default, 4) of these small sstables, we compact them into one medium sstable.
3.  When we have collected enough medium tables, we compact them into one large table. 
4. And so on, with compacted sstables growing increasingly large.

上述过程可以用下图描述（每一次的文件之间都可能有Overlap）：

```
Memtable ->
	Size * 1 SST Table
	Size * 1 SST Table
	Size * 1 SST Table
	Size * 1 SST Table
		Size * 4 SST Table
		Size * 4 SST Table
		Size * 4 SST Table
		Size * 4 SST Table
			Size * 16 SST Table
			Size * 16 SST Table
			Size * 16 SST Table
			Size * 16 SST Table
			...
```


### 合并SST Table的选取

可以认为所有SST文件只有一层。只能选择相邻文件进行合并，合并后仍处于原位置，就可以保证查询数据的正确。


### 放大分析

- 因为每次向第N层compaction时不需要读取所有N层与N-1层需Compaction文件范围相交的所有文件，排序合并后再输出，而是直接把N-1层的文件合并后就输出，可以有效减少写放大。

- 但是每一层的文件之间都有Overlap，查询开销变大

- 空间放大也比Level Compaction大，最大可达2.Level Compaction一般为1.3。

- Double Size Issue：In universal style compaction, sometimes full compaction is needed. In this case, output data size is similar to input size. During compaction, both of input files and the output file need to be kept, so the DB will be temporarily double the disk space usage. 

