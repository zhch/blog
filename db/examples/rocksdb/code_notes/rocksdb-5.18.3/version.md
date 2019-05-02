### Decode Manifest

DecodeFrom是核心方法，调用关系分析：
```
VersionEdit::DecodeFrom
	VersionSet::Recover
		DBImpl::Recover
			DB::Open
		VersionSet::ReduceNumberOfLevels
	VersionSet::ListColumnFamilies
		DB::ListColumnFamilies
	VersionSet::DumpManifest
		tools/ldb_cmd.cc#DumpManifestFile()
```

VersionSet::DumpManifest逻辑分析：

```
VersionSet::DumpManifest
	Reader::ReadRecord
		int Reader::ReadPhysicalRecord
	VersionEdit::DecodeFrom
```

### VersionSet Recover

```C++
读取CURRENT文件
VersionEdit default_cf_edit;
default_cf_edit.AddColumnFamily(kDefaultColumnFamilyName);
default_cf_edit.SetColumnFamily(0);
ColumnFamilyData* default_cfd = 
    CreateColumnFamily(default_cf_iter->second, &default_cf_edit);
builders.insert({0, new BaseReferencedVersionBuilder(default_cfd)});
while(log::Reader::ReadRecord())
    
    

```


### VersionSet的修改
可以根据下面这个创建ColumFamily的栈，overview VersionSet的修改过程：

```C++
#0  hs::kv::MemTable::MemTable (this=0x7fb994011860, cmp=..., ioptions=..., mutable_cf_options=..., write_buffer_manager=0x3cb6cb0, latest_seq=0, column_family_id=1)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/memtable.cc:88
#1  0x0000000002027593 in hs::kv::ColumnFamilyData::ConstructNewMemtable (this=0x7fb99400db50, mutable_cf_options=..., earliest_seq=0)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/column_family.cc:750
#2  0x000000000202762c in hs::kv::ColumnFamilyData::CreateNewMemtable (this=0x7fb99400db50, mutable_cf_options=..., earliest_seq=0)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/column_family.cc:758
#3  0x0000000001d90b1b in hs::kv::VersionSet::CreateColumnFamily (this=0x3cbe6a0, cf_options=..., edit=0x7fb9d745c6e0)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/version_set.cc:3569
#4  0x0000000001d89aa2 in hs::kv::VersionSet::LogAndApply (this=0x3cbe6a0, column_family_data=0x0, mutable_cf_options=..., edit_list=..., mu=0x3cb6228, db_directory=0x3cc0b80, new_descriptor_log=false, 
    new_cf_options=0x7fb9d745cee0) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/version_set.cc:2462
#5  0x0000000001c85248 in hs::kv::VersionSet::LogAndApply (this=0x3cbe6a0, column_family_data=0x0, mutable_cf_options=..., edit=0x7fb9d745c6e0, mu=0x3cb6228, db_directory=0x3cc0b80, new_descriptor_log=false, 
    column_family_options=0x7fb9d745cee0) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/version_set.h:594
#6  0x0000000001c6fb56 in hs::kv::DBImpl::CreateColumnFamilyImpl (this=0x3cb5d70, cf_options=..., column_family_name=..., handle=0x7fb9d745ce98)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/db_impl.cc:1174
#7  0x0000000001c6f3bc in hs::kv::DBImpl::CreateColumnFamily (this=0x3cb5d70, cf_options=..., column_family=..., handle=0x7fb9d745ce98)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/db/db_impl.cc:1079
#8  0x0000000001af7f55 in hs::KVStore::CreateCF (this=0x3cb2e40, name=...) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/kv/KVStore.cpp:294
#9  0x00000000019a308e in hs::RCTable::CreateNew (opt=..., form=0x7fb9d745df20) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/core/RCTable.cpp:101
#10 0x00000000018ccf84 in hs::Engine::CreateTable (this=0x3c977c0, table=..., form=0x7fb9d745df20) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/core/Engine.cpp:684
#11 0x000000000200fbb2 in hs::HistoreHandler::create (this=0x7fb99400a070, name=0x7fb9d745ede0 "./perf_test_update/update_comp", table_arg=0x7fb9d745df20, create_info=0x7fb9d745f620)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/storage/histore/handler/HistoreHandler.cpp:1023
#12 0x00000000014a0214 in handler::ha_create (this=0x7fb99400a070, name=0x7fb9d745ede0 "./perf_test_update/update_comp", form=0x7fb9d745df20, info=0x7fb9d745f620)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/handler.cc:4544
#13 0x00000000014a0a74 in ha_create_table (thd=0x44ab750, path=0x7fb9d745ede0 "./perf_test_update/update_comp", db=0x7fb994004420 "perf_test_update", table_name=0x7fb994003e80 "update_comp", 
    create_info=0x7fb9d745f620, update_create_info=false, is_temp_table=false) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/handler.cc:4782
#14 0x00000000016e48dc in rea_create_table (thd=0x44ab750, path=0x7fb9d745ede0 "./perf_test_update/update_comp", db=0x7fb994004420 "perf_test_update", table_name=0x7fb994003e80 "update_comp", 
    create_info=0x7fb9d745f620, create_fields=..., keys=1, key_info=0x7fb9940059a0, file=0x7fb9940054a8, no_ha_table=false) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/unireg.cc:527
#15 0x0000000001685fd4 in create_table_impl (thd=0x44ab750, db=0x7fb994004420 "perf_test_update", table_name=0x7fb994003e80 "update_comp", path=0x7fb9d745ede0 "./perf_test_update/update_comp", 
    create_info=0x7fb9d745f620, alter_info=0x7fb9d745f0c0, internal_tmp_table=false, select_field_count=0, no_ha_table=false, is_trans=0x7fb9d745f06e, key_info=0x7fb9d745efe8, key_count=0x7fb9d745efe4)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_table.cc:4930
#16 0x00000000016862ae in mysql_create_table_no_lock (thd=0x44ab750, db=0x7fb994004420 "perf_test_update", table_name=0x7fb994003e80 "update_comp", create_info=0x7fb9d745f620, alter_info=0x7fb9d745f0c0, 
    select_field_count=0, is_trans=0x7fb9d745f06e) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_table.cc:5040
#17 0x00000000016863aa in mysql_create_table (thd=0x44ab750, create_table=0x7fb994003ec8, create_info=0x7fb9d745f620, alter_info=0x7fb9d745f0c0)
    at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_table.cc:5089
#18 0x0000000001620b5b in mysql_execute_command (thd=0x44ab750) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_parse.cc:3080
#19 0x0000000001629830 in mysql_parse (thd=0x44ab750, 
    rawbuf=0x7fb994003ca0 "CREATE TABLE update_comp ( id BIGINT, col1 INT, col2 INT, col3 INT, col4 INT, col5 DECIMAL(5,2), col6 FLOAT, col7 CHAR(8), col8 VARCHAR(64), PRIMARY KEY(id)) ENGINE=HISTORE", 
    length=172, parser_state=0x7fb9d7460110) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_parse.cc:6428
#20 0x000000000161ceeb in dispatch_command (command=COM_QUERY, thd=0x44ab750, 
    packet=0x44aea91 "CREATE TABLE update_comp ( id BIGINT, col1 INT, col2 INT, col3 INT, col4 INT, col5 DECIMAL(5,2), col6 FLOAT, col7 CHAR(8), col8 VARCHAR(64), PRIMARY KEY(id)) ENGINE=HISTORE;", 
    packet_length=173) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_parse.cc:1342
#21 0x000000000161c023 in do_command (thd=0x44ab750) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_parse.cc:1039
#22 0x00000000015e81f1 in do_handle_one_connection (thd_arg=0x44ab750) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_connect.cc:982
#23 0x00000000015e7d55 in handle_one_connection (arg=0x44ab750) at /disk8/wanghu/histore/dev/histore/src/mysql-5.6.24/sql/sql_connect.cc:898
#24 0x00007fc9c0d2fe25 in start_thread () from /lib64/libpthread.so.0
```