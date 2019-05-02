# WriteBatch

## Classes

* WriteBatchInternal: 封装不希望在外部头文件中出现的接口

## WriteBatch类
### 有用的内部字段

```C++
std::string rep_;  
SavePoints* save_points_;
SavePoint wal_term_point_;
mutable std::atomic<uint32_t> content_flags_;
size_t max_bytes_;
bool is_latest_persistent_state_ = false;
```

### rep_
所有内容内部都存储在rep_这个std::string当中，rep_ 格式：

```
// WriteBatch::rep_ :=
//    sequence: fixed64
//    count: fixed32
//    data: record[count]
// record :=
//    kTypeValue varstring varstring
//    kTypeDeletion varstring
//    kTypeSingleDeletion varstring
//    kTypeRangeDeletion varstring varstring
//    kTypeMerge varstring varstring
//    kTypeColumnFamilyValue varint32 varstring varstring
//    kTypeColumnFamilyDeletion varint32 varstring
//    kTypeColumnFamilySingleDeletion varint32 varstring
//    kTypeColumnFamilyRangeDeletion varint32 varstring varstring
//    kTypeColumnFamilyMerge varint32 varstring varstring
//    kTypeBeginPrepareXID varstring
//    kTypeEndPrepareXID
//    kTypeCommitXID varstring
//    kTypeRollbackXID varstring
//    kTypeBeginPersistedPrepareXID varstring
//    kTypeBeginUnprepareXID varstring
//    kTypeNoop
// varstring :=
//    len: varint32
//    data: uint8[len]
```