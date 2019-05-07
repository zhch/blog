## Tiered Compaction

也叫size tiered compaction，RocksDB中叫Universal Compaction。

### 基本算法

最基本的流程如下：

1. memtables are periodically flushed to new sstables. These are pretty small, and soon their number grows.
2. As soon as we have enough (by default, 4) of these small sstables, we compact them into one medium sstable.
3.  When we have collected enough medium tables, we compact them into one large table. 
4. And so on, with compacted sstables growing increasingly large.

上述过程可以用下图描述：

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

