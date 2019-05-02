RocksDB提供下面这一系列接口，供用户获取DB/ColumnFamily目前处于的状态

```C++
bool GetProperty(ColumnFamilyHandle* column_family, const Slice& property, std::string* value);
bool GetProperty(const Slice& property, std::string* value);
bool GetMapProperty(ColumnFamilyHandle* column_family, const Slice& property, std::map<std::string, std::string>* value);
bool GetMapProperty(const Slice& property, std::map<std::string, std::string>* value);
bool GetIntProperty(ColumnFamilyHandle* column_family, const Slice& property, uint64_t* value);
bool GetIntProperty(const Slice& property, uint64_t* value);
bool GetAggregatedIntProperty(const Slice& property, uint64_t* value);
Status ResetStats();
```



所有支持的Property列表位于include/rocksdb/db.h中的struct Properties{}。

Property查询最终都由InternalStates类实现 。

InternalStats::ppt_name_to_info字段保存了如何根据Propety Name进行相关查询的回调函数列表。

以下面所示的Compaction Stats的查询Stack如下：

```

** Compaction Stats [1@ALL] **
Level    Files   Size     Score Read(GB)  Rn(GB) Rnp1(GB) Write(GB) Wnew(GB) Moved(GB) W-Amp Rd(MB/s) Wr(MB/s) Comp(sec) Comp(cnt) Avg(sec) KeyIn KeyDrop
----------------------------------------------------------------------------------------------------------------------------------------------------------
  L0      4/0   95.59 MB   1.0      0.0     0.0      0.0       0.1      0.1       0.0   1.0      0.0     66.3         1         4    0.360       0      0
 Sum      4/0   95.59 MB   0.0      0.0     0.0      0.0       0.1      0.1       0.0   1.0      0.0     66.3         1         4    0.360       0      0
 Int      0/0    0.00 KB   0.0      0.0     0.0      0.0       0.0      0.0       0.0   0.0      0.0      0.0         0         0    0.000       0      0
Uptime(secs): 26.8 total, 0.0 interval
Flush(GB): cumulative 0.093, interval 0.000
AddFile(GB): cumulative 0.000, interval 0.000
AddFile(Total Files): cumulative 0, interval 0
AddFile(L0 Files): cumulative 0, interval 0
AddFile(Keys): cumulative 0, interval 0
Cumulative compaction: 0.09 GB write, 3.56 MB/s write, 0.00 GB read, 0.00 MB/s read, 1.4 seconds
Interval compaction: 0.00 GB write, 0.00 MB/s write, 0.00 GB read, 0.00 MB/s read, 0.0 seconds
Stalls(count): 0 level0_slowdown, 0 level0_slowdown_with_compaction, 0 level0_numfiles, 0 level0_numfiles_with_compaction, 0 stop for pending_compaction_bytes, 0 slowdown for pending_compaction_bytes, 0 memtable_compaction, 0 memtable_slowdown, interval 0 total count
```





```C++
DumpCFMapStats
	DumpCFStatsNoFileHistogram
	File Read Latency Histogram By Level
		DumpCFFileHistogram	
			DumpCFStats	
				InternalStats::HandleCFStats
					InternalStats::ppt_name_to_info
					InternalStats::HandleStats
						InternalStats::ppt_name_to_info
							GetPropertyInfo(kCFStats)
								MaybeDumpStats
								DBImpl::GetProperty
								DBImpl::GetMapProperty
								DBImpl::GetIntProperty
								DBImpl::GetAggregatedIntProperty
```