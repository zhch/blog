读写主类：

rocksdb/db/log_writer.h

rocksdb/db/log_reader.h



Log format information shared by reader and writer：rocksdb/db/log_format.h



Legacy record format:

```
 * +---------+-----------+-----------+--- ... ---+
 * |CRC (4B) | Size (2B) | Type (1B) | Payload   |
 * +---------+-----------+-----------+--- ... ---+
```



Recyclable record format:

```
 * +---------+-----------+-----------+----------------+--- ... ---+
 * |CRC (4B) | Size (2B) | Type (1B) | Log number (4B)| Payload   |
 * +---------+-----------+-----------+----------------+--- ... ---+
```



如何判断是否Recyclable record format:

根据type字段，type >= kRecyclableFullType && type <= kRecyclableLastType

