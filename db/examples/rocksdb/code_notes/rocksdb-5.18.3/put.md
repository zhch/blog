# Core APIs



回顾：

1. Memory Order优化总结：WriterThread::newest_writer_、DBImpl::content_flag
2. ifndef __clang_analyzer__



1. 

   




## 写数据

### API梳理
写数据最核心的是两个API，Put一对KV，或者是Write一个WriteBatch，内部其实只有Write一个统一的API：

```C++
Status DB::Put(const WriteOptions& opt, ColumnFamilyHandle* column_family, const Slice& key, const Slice& value)
CALL: Status DBImpl::Write(const WriteOptions& write_options, WriteBatch* my_batch)
```

核心实现API是DBImpl::WriteImpl:

```C++
Status DBImpl::WriteImpl(
    const WriteOptions& options, 
    WriteBatch* updates,
    WriteCallback* callback = nullptr,
    uint64_t* log_used = nullptr, 
    uint64_t log_ref = 0,
    bool disable_memtable = false, 
    uint64_t* seq_used = nullptr,
    size_t batch_cnt = 0,
    PreReleaseCallback* pre_release_callback = nullptr
)
```

- log_used: 如果不为nullptr，返回该WriteBatch最终使用的log number
- seq_used: 如果不为nullptr，返回该WriteBatch最终使用的SequenceNumber



执行过程中各个flags的常用取值

| flag                                         | 常用取值 | 含义                                                       |
| -------------------------------------------- | -------- | ---------------------------------------------------------- |
| immutable_db_options_.enable_pipelined_write |          | https://github.com/facebook/rocksdb/wiki/Pipelined-Write|
|                                              |          |                                                            |
|                                              |          |                                                            |
|                                              |          |                                                            |
|                                              |          |                                                            |





| 函数                                                         | 作用                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| size_t WriteThread::EnterAsBatchGroupLeader(Writer* leader, WriteGroup* write_group) | 以参数leader为首，遍历Writer队列，加入参数给定的WriteGroup |
|                                                              |                                                            |
|                                                              |                                                            |
|                                                              |                                                            |
|                                                              |                                                            |
|                                                              |                                                            |
|                                                              |                                                            |

主体逻辑：

1. 参数检查
2. low priority write限流逻辑
3. 如果使用两个写队列，并且跳过写Memtable，则直接跳到WriteImplWALOnly()执行
4. 如果使用Pipeline写则直接跳到PipelinedWriteImpl()执行
5. 新建WriteThread::Writer对象
6. 调用WriteThread::JoinBatchGroup()加入DBImpl::write_thread_的队列
7. TODO: if (w.state == WriteThread::STATE_PARALLEL_MEMTABLE_WRITER)
8. 

Checking:

1. SwitchMemtable
2. 








## 入Memtable

总入口 在（check parallel case）WriteBatchInternal::InsertInto

```c++
WriteBatchInternal::InsertInto
	write_batch.cc#Status WriteBatch::Iterate(Handler* handler) const
    	class MemTableInserter : public WriteBatch::Handler 的各个回调接口
    		
```



```
write_batch.cc#MemTableInserter#PutCF()
	... ...
	回调MemTableRep各个接口
		MemTableRep各种数据结构实现位于memtable目录
	CheckMemtableFull
```



- 默认Memtable实现memtable/skiplistrep.cc: hs::kv::SkipListRep
- SkipListRep的跳表实现：memtable/inlineskiplist.h








## rocksdb::DBImpl核心内部字段

| 字段              | 类型       | 含义                                                         |      |
| ----------------- | ---------- | ------------------------------------------------------------ | ---- |
| two_write_queues_ | const bool | uses two queues for writes, one for the ones with disable_memtable and one for the ones that also write to memtable. This allows the memtable writes not to lag behind other writes. |      |
|                   |            |                                                              |      |
|                   |            |                                                              |      |
|                   |            |                                                              |      |
|                   |            |                                                              |      |
|                   |            |                                                              |      |
|                   |            |                                                              |      |





Something Obvious:
```
  Env* const env_;
  const std::string dbname_;
  std::unique_ptr<VersionSet> versions_;
  const DBOptions initial_db_options_;
  const ImmutableDBOptions immutable_db_options_;
    MutableDBOptions mutable_db_options_;
  Statistics* stats_;
```









~