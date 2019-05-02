```C++
Status ListColumnFamilies(
    const DBOptions& db_options, 
    const std::string& name, std::vector<std::string>* column_families
);
```



列出当前DB所有ColumnFamily Name，通过读取MANIFEST实现。



读取ColumnFamily Name之后就可以配置ColumnFamily相关配置，并创建ColumnFamilyDescriptor：

```C++
ColumnFamilyOptions cf_option(options);
std::vector<ColumnFamilyDescriptor> cf_descr;
for (auto &cf_name : cf_names) {
	cf_descr.push_back(ColumnFamilyDescriptor(cf_name, cf_option));
}
```



最后，指定以ColumnFamily模式打开DB：

```C++
Status Open(
    const DBOptions& db_options, 
    const std::string& name, 
    const std::vector<ColumnFamilyDescriptor>& column_families,
    std::vector<ColumnFamilyHandle*>* handles, 
    DB** dbptr
)
```



- db_options specify database specific options
- column_families is the vector of all column families in the database,
- containing column family name and options. 
- You need to open ALL column families in the database. 
- To get the list of column families, you can use ListColumnFamilies(). 
- Also, you can open only a subset of column families for read-only access.
- The default column family name is 'default' and it's stored in rocksdb::kDefaultColumnFamilyName.
- If everything is OK, handles will on return be the same size as column_families --- handles[i] will be a handle that you will use to operate on column family column_family[i].
- Before delete DB, you have to close All column families by calling DestroyColumnFamilyHandle() with all the handles.